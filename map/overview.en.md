# Map component

The core of the application will be a navigation map enhanced by custom data coming from various sources depending on the user context.
After some investigations, I think we can tell that such an application will require the following components:

## Map tiles server

[Map tiles](https://en.wikipedia.org/wiki/Tiled_web_map) are parts of a map for a given level of zoom. They are usually images drawn based on **geometrical data** together with tags, following a **given style**.
The sources of these data can be diverse but a good starting point could be [Open Street Map](https://en.wikipedia.org/wiki/OpenStreetMap). Such data usually refer to a geometrical shape (point, line, polygon)
associated with tags. Example:

osm_id | ... | highway | name | operator | ... | way
-------| --- | --------| ---- | -------- | --- | ---
5435.. | ... | primary | 5 Avenida | Omnibus Metropolitanos | ... | Line(82.4545, 82.345435)

The displayed row has an unique identifier, a list of tags with some values (shortened for brevity) and its geometrical structure.
In our example we refer to a route of type *primary* defined by a *line*, and the tag *operator* tell us that this stretch is part of a bus line operated by *Omnibus Metropolitanos*

You'll notice that a same physical line can refer to semantically different concepts (road, bus line, etc).

If we use the geometry property (*way*) we could draw this database row.

For example, drawing all the lines from the database gives us roughly the network of roads and path of the area.

![osm-planet_line drawn](../media/drawn-image.png)

A drawing software such [Mapnik](https://wiki.openstreetmap.org/wiki/Mapnik) will use this data and style instructions to generate images that can be then served with a regular file server
offering all the benefits of dealing with static files (cache, CDN distribution, etc).

For information, the whole data set for the city of La Havana is about 70mb to download which is relatively small.

Some services like [MapBox](https://www.mapbox.com/) do not use map tile images but rather [vector tiles](https://docs.mapbox.com/vector-tiles/). The idea is quite similar, but instead of using already rendered static files sent from the server to a client application,
the server will send vector data which will then be used by a renderer in the client application: this gives more flexibility as the map can be dynamically rendered within the client application using
some provided Software Development Kit (SDK) adapted to the platform (Android, Web, IOS, etc).

## Routing machine API

A [Routing machine](https://wiki.openstreetmap.org/wiki/Routing)(or Router) is required to calculate itinerary between various points. From what I understood, it usually uses algorithms to compute path on
a graph based on the network of roads and the locomotion way used by the user. The vertex may carry some additional weight depending on various factors (road surface, traffic, slope, etc) in
order to calculate an optimized itinerary.

A routing machine can involve advanced mathematics and therefore open source routing machine exist so tiers web service do.

## Geo encoding API

A [Geo encoding API](https://en.wikipedia.org/wiki/Geocoding) will help in translating textual information (like an address or the name of place) into geographical coordinates (longitude, latitude).
It can also do opposite operation: find out whether a set of coordinates matches a known place or address.

- Such API usually involve building [inverted index](https://en.wikipedia.org/wiki/Inverted_index) of known places so they suggest relevant matches on fuzzy textual searches. For example if one searches for "plaza de la revolucion", "plaza revoluzion", "plaza rev", etc (or with typos). The API should return the actual *plaza de la revolucion* with high relevance score.
- They also use algorithms and parsers in order to draw address information from a raw string address. I am not really familiar with what its involve behind though.

Third party web services exist but I have not found any so far which perform really well on La Habana address scheme (or lack of scheme). This area needs to be investigated more thoroughly.

### Implementation

Running a tile server of or own is feasible with available open source tools. Open source Routing machine/services also exist, so geo encoding services do.
Plenty of third party services or open sources projects can be found in the Open Street Map.
However I think managing all these components on our own would be an extra overhead and troublesome considering our budget and the human resources we have at our disposition.
Moreover some area of the problems are more or less complicated and although quite interesting, it would be a waste of time to try to reinvent the wheel and try to solve on our own problems already more or less solved.

*I believe we can start with a third party service (or various third party services) first and maybe change it later for our own implementation or server instances if we find out that it would be more cost efficient.**

### Mapbox

The one I have selected is [Mapbox](https://www.mapbox.com/) which is well established in the field. It offers all the required components and shall have a high level of availability. Its free tier is generous enough
to handle our load at the beginning at least.

The map service has a high level of style customization and does not require specific skill to manage the style (they have an [online editor]()).
A map is designed in layers with an associated style. They also allow the management (through API) of custom data set we can associated to a given layer. This can be very helpful to
group all map related data in the same place and to automate data update.
