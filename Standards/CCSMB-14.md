# CCSMB-14: Standardised modem channels

## Rationale

The most popular servers have *a lot* of modem traffic on various unspecified channels. This aims to create dedicated channels for dedicated usages, in order to prevent applications from sending messages that might break other applications.

## Channel allocation

|Channel      | Program / Protocol     | Author      | Details  |
|-------------|------------------------|-------------|----------|
| 0-65535     | Rednet                 | CraftOS     | Rednet uses these channels to communicate with other computers  |
| 999         | Opus OS                |  kepler155c |  Host discovery |
| 3636        | Lightweight Messaging  | Gollark     | p2p chat system |
| 65500-65531 | CCSMB-13               | Drucifer    | Radio channels usage|