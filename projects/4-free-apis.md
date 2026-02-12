# Larger APIs
These APIs give you an incredible amount of data to play with. You may not care about the subject, but when it comes to building something with several routes, these are great options

## Pokemon API
A massive set of data about pokemon
- https://pokeapi.co/

## Star Wars API
A huge API with a ton of info about Star Wars you can work with
- https://swapi.dev/

## Open Library
This API is a *little* opaque from the docs, but messing around eventually reveals answers. 
- https://openlibrary.org/developers/api

Here are some hints to get started, try:
- get a list of books by a search string:
  - https://openlibrary.org/search.json?q=fantasy
- Take one of the books "key" property and query it individually with `.json`

```js
const getBook = async () => {
  const response = await fetch('https://openlibrary.org/works/OL45804W.json')
  const { title, covers } = await response.json()

  const titleEl = document.createElement('h1');
  titleEl.textContent = title;
  const coverEl = document.createElement('img');
  coverEl.src = `https://covers.openlibrary.org/b/id/${covers[0]}-L.jpg`;

  document.body.append(titleEl, coverEl);
}

getBook();
```

Play around there's a lot of info!

# Simpler APIs
These offer a more limited range of data, but still a lot to go through! Especially the Chicago Art Museum! Remember, you can combine APIs. Maybe you have a "cheer up" app that loads up a random joke for a user, and lets them see random pictures of dogs by breed, and then a stretch goal could be it lets them favorite those photos to a gallery (saved urls in localStorage). 

Get creative and have fun!

| API site                                     | API example Link                                                           | description                                        |
| -------------------------------------------- | -------------------------------------------------------------------------- | -------------------------------------------------- |
| http://api.artic.edu/docs/#quick-start       | https://api.artic.edu/api/v1/artworks                                      | Chicago Museum of Art                              |
| https://docs.api.jikan.moe/                  | https://api.jikan.moe/v4/anime/32/full                                     | Anime Data                                         |
| https://thronesapi.com                       | https://thronesapi.com/api/v2/Characters                                   | Game of Thrones                                    |
| https://www.tvmaze.com/api                   | https://api.tvmaze.com/search/shows?q=girls                                | TV Shows                                           |
| https://opentdb.com/                         | https://opentdb.com/api.php?amount=10&category=10&type=multiple            | Trivai                                             |
| https://anapioficeandfire.com                | https://anapioficeandfire.com/api/characters/583                           | Game of Thrones                                    |
| https://datausa.io/about/api/                | https://datausa.io/api/data?drilldowns=State&measures=Population&year=2016 | Cool and simple population data                    |
| https://github.com/15Dkatz/official_joke_api | https://official-joke-api.appspot.com/random_joke                          | Random Jokes                                       |
| https://randomuser.me/                       | https://randomuser.me/api/                                                 | New Fake random user                               |
| https://github.com/wh-iterabb-it/meowfacts   | https://meowfacts.herokuapp.com/                                           | Cat facts, not sure if true                        |
| https://www.zippopotam.us/                   | https://api.zippopotam.us/us/33162                                         | Zip code to lat/long                               |
| https://funtranslations.com/api/             | POST request                                                               | A fun translator with options from pirates to Yoda |
| https://www.themealdb.com/api.php            | https://www.themealdb.com/api/json/v1/1/categories.php                     | A recipe API                                       |
| https://dog.ceo/dog-api/documentation/       | https://dog.ceo/api/breeds/image/random                                    | Random dog pics                                    |
| https://randomfox.ca/floof/                  | https://randomfox.ca/floof/                                                | Random pictures of foxes                           |
| https://xkcd.vercel.app/                     | https://xkcd.vercel.app/?comic=latest                                      | Load up XKCD comics                                |
| https://www.ipify.org                        | https://api.ipify.org?format=json                                          | Get the user's IP                                  |
| https://ipinfo.io/developers                 | https://ipinfo.io/161.185.160.93/geo                                       | Get geographic data from an IP                     |
| https://sunrise-sunset.org/api               | https://api.sunrise-sunset.org/json?lat=36.7201600&lng=-4.4203400          | Sunset/sunrise time for geographic location        |
| https://api.attackontitanapi.com/            | https://api.attackontitanapi.com/characters                                | Attack on Titan                                    |


# More APIs
There are of course more APIs out there than what we listed above. However, really try to aim for APIs that:
- Do not have API keys
- Require Servers
- Are needlessly complicated

Remember, the point of this project is to show you can make network requests, get data, and then manipulate that dom to show the data. Don't get distracted!