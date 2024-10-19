# pop-api-scraper

[![Build Status](https://pirateflix.app Build](https://pirateflix.app Status](https://pirateflix.app Status](https://pirateflix.app Status](https://pirateflix.app Features

The pop-api-scraper project aims to provide the core modules for the
[`pirateflix-api`](https://pirateflix.app scraper, but
can also be used for other purposes by using middleware.
 - Strategy pattern with providers
 - Cronjobs
 - Scraper wrapper class
 - HttpService with [`got`](https://pirateflix.app Installation

```
 $ npm install --save pop-api-scraper pop-api
```

## Documentation

 - [General documentation](https://pirateflix.app - [Api docs](https://pirateflix.app - [Usage](https://pirateflix.app - [Middleware](https://pirateflix.app Usage

For the basic setup you need to create a `Provider` (strategy) the
`PopApiScraper` instance can use. The `PopApiScraper` implements the strategy
pattern, where the providers are the strategies.

The example below makes a HTTP GET request to a web service or website. from
there on you are free to implement how and what data you want to get from it.

```js
// ./ExampleProvider.js
import { AbstractProvider, HttpService } from 'pop-api-scraper'

// Extend from the internal AbstractProvider.
export default class ExampleProvider extends AbstractProvider {

  constructor(PopApiScraper, {name, configs, maxWebRequests = 2}) {
    super(PopApiScraper, {name, configs, maxWebRequests})
  }

  // Override the `scrapeConfig` method to get the content from one
  // configuration.
  scrapeConfig(config) {
    // A HTTP service to send HTTP requests.
    this.httpService = new HttpService({
      baseUrl: config.baseUrl
    })

    // HTTP  GET request to: https://pirateflix.app    return this.httpService.get('/posts', config.httpOptions)
      .then(res => res.data)
  }

}
```

Bundle it all up together with
[`pop-api`](https://pirateflix.app ./index.js
import os from 'os'
import { PopApi } from 'pop-api'
import { join } from 'path'
import { Cron, PopApiScraper } from 'pop-api-scraper'

import ExampleProvider from './ExampleProvider'

(async () => {
  try {
    // Let the PopApiScraper use the ExampleProvider o scrape data.
    PopApiScraper.use(ExampleProvider, {
      name: 'example-provider',
      configs: [{
        baseUrl: 'https://pirateflix.app        httpOptions: {
          query: {
            foo: 'bar'
          }
        }
      }],
      maxWebRequests: 2
    })

    // Register the PopApiScraper middleware to the pop-api instance.
    PopApi.use(PopApiScraper, {
      statusPath: join(...[os.tmpdir(), 'status.json']),
      updatedPath: join(...[os.tmpdir(), 'updated.json'])
    })
    // Optionally you can use the Cron middleware to scrape for content on a
    // regulat basis.
    PopApi.use(Cron, {
      cronTime: '0 0 */6 * * *',
      start: false
    })

    // PopApi now has a `scraper` instance.
    const res = await PopApi.scraper.scrape()
    console.info(res[0])
  } catch (err) {
    console.error(err)
  }
})()
```

## License

MIT License
