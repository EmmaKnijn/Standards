# CCSMB-5: PCM Audio Transmissions over modem (PASC)
*Author: Emma Beurskens <@emmaknijn>*
*Version: v1.0.0*
*Last updated: YYYY-MM-DD*

## Design
A client will only listen to packets while the audio is being transmitted from a server

The server will communicate to the client over modem

All servers must be on seperate channels

PASC refers to "Protocol for Audio Streaming in Computercraft"

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED",  "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC 2119.

## Packet
An audio packet consists of the following fields in a table:
- `buffer`: A table that contains up to 8 tables of signed 8-bit PCM @ 48kHz audio
- `id`: The ID of the server sending the message
- `station`: A string that contains the name of the radio station
- `metadata`: A table that contains additional metadata
- `protocol`: A string that contains the protocol used


The metadata MAY contain the following fields:
- `song`: A string that contains the song name, if one is being played, not required
- `artist`: A string that contains the song artist, if a song is being played, not required
- `owner`: A string that contains the station owner's (ingame or discord) username. The discord username should start with an @ to identify it from an ingame username, not required
- `album`: A string that contains the name of the album the song is in, not required
- `alternative`: A table that contains alternative station channels, with integers, inside, not required

A valid packet should look like this. Being transmitted on channel `5836` with replyChannel `759`

```lua
{
  buffer = {
    {... PCM DATA ...},
    {... PCM DATA ...}
  },
  id = 1234,
  station = "Knijn Radio",
  protocol = "PASC",
  metadata = {
    song = "BOOWOMP",
    artist = "Ditorism",
    album = "BATTERY ACID",
    owner = "@emmaknijn",
    alternative = {5836, 5837, 7956}
  }
}
```

In order to be compliant the
- protocol field MUST be "PASC"
- client MUST be able to deal with crosstalk by allowing the user to select a station if there are multiple stations on a single channel
- server MUST set the replyChannel to 759, the discovery channel
- client MUST reject a packet with an invalid `protocol` field
- server MUST include all broadcasting channels in the `metadata.alternative` field (including the channel the packet is being sent on), except if no `metadata.alternative` field is provided.
- server MUST time the packets appropriately in a way that the client is just able to play back the buffer.
- server MUST wait exactly as long as the piece of audio being sent when timing the packets, if this doesn't happen the audio might skip or stutter on the client. The recommended amount for a single packet is about 2.5 seconds
- client MUST be able to support 2 audio channels and MAY be able to support 3-8 audio channels.
- server MUST order the first 2 audio channels of the buffer to
  ```
  buffer[1] = leftChannel,
  buffer[2] = rightChannel
  ```
- client MUST play back the first buffer as the left channel, and the second buffer as the right channel
- client SHOULD mix audio channels 1 and 2 together if there is only 1 speaker connected
- client SHOULD copy audio channel 1 to the left and right audio channel if there are two speakers connected

The other audio channels are free to be assigned to whatever is desired

## Discovery
A discovery packet MUST be sent over channel 759 and MUST contain the following fields in a table:
- `type`: The type of request

The following fields are required only for servers responding to a discovery packet
- `channel`: An integer that contains the modem channel of the radio station
- `station`: A string that contains the name of the radio station
- `metadata`: The same metadata that is also sent with an audio packet
- `protocol`: A string that contains the protocol used

In order to be compliant the
- protocol field must be "PASC"
- client must set the `type` field to `discovery` if they intend to request stations
