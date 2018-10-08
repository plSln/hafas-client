# `journeysFromTrip(tripId, previousStopover, to, [opt])`

`from` and `to` each must be an FPTF `1.2.0` [`location`](https://github.com/public-transport/friendly-public-transport-format/blob/1.2.0/spec/readme.md#location-objects), [`stop`](https://github.com/public-transport/friendly-public-transport-format/blob/1.2.0/spec/readme.md#stop) or [`station`](https://github.com/public-transport/friendly-public-transport-format/blob/1.2.0/spec/readme.md#station). See [`journeys()`](journeys.md) for details.

With `opt`, you can override the default options, which look like this:

```js
{
	// todo
}
```

## Response

*Note:* As stated in the [*Friendly Public Transport Format* `1.2.0`](https://github.com/public-transport/friendly-public-transport-format/tree/1.2.0), the returned `departure` and `arrival` times include the current delay. The `departureDelay`/`arrivalDelay` fields express how much they differ from the schedule.

As an example, we're going to use the [DB profile](../p/db):

```js
const createClient = require('hafas-client')
const dbProfile = require('hafas-client/p/db')

const berlinSüdkreuz = '8011113'
const münchenHbf = '8000261'
const kölnHbf = '8000207'

const client = createClient(dbProfile, 'my-awesome-program')

// find any journey from Berlin Südkreuz to München Hbf
const [journey] = await client.journeys(berlinSüdkreuz, münchenHbf, {results: 1, stopovers: true})
// find the ICE leg
const leg = journey.legs.find(l => l.line.product === 'nationalExp')
// find the stopover at the stop you've just passed
const previousStopover = leg.stopovers.find(st => st.departure && new Date(st.departure) < Date.now())

// find journeys from the ICE train to Köln Hbf
const journeys = await client.journeysFromTrip(leg.id, previousStopover, kölnHbf)
```

`journeys` will be an array of [FPTF `1.2.0` `journey`s](https://github.com/public-transport/friendly-public-transport-format/blob/1.2.0/spec/readme.md#journey), as documented in [`journeys()`](journeys.md).