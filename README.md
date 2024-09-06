[![scrape-it](https://i.imgur.com/j3Z0rbN.png)](#)

# Dev_Scrap

<a href="https://www.buymeacoffee.com/H96WwChMy" target="_blank" class="linkedin-button">
  <img src="https://upload.wikimedia.org/wikipedia/commons/c/ca/LinkedIn_logo_initials.png" alt="Follow on LinkedIn" />
</a>

<style>
.linkedin-button {
  display: inline-block;
  background-color: #0077b5; /* LinkedIn blue */
  padding: 8px;
  border-radius: 5px;
  transition: background-color 0.3s ease;
  text-decoration: none;
}

.linkedin-button img {
  width: 40px; /* Size of the LinkedIn logo */
  height: 40px;
  vertical-align: middle;
}

.linkedin-button:hover {
  background-color: #005582; /* Darker LinkedIn blue on hover */
}
</style>

> A Node.js scraper by Dev

## :cloud: Installation

```sh
# Using npm
npm install --save scrape-it

# Using yarn
yarn add scrape-it
```

:bulb: ProTip: You can install the CLI version of this module by running npm install --global scrape-it-cli (or yarn global add scrape-it-cli).

Social Links

<div class="social-links"> <a href="https://github.com/debrajrout" target="_blank" class="social-button github"> <img src="https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png" alt="GitHub" /> </a> <a href="https://www.linkedin.com/in/debarajrout/" target="_blank" class="social-button linkedin"> <img src="https://upload.wikimedia.org/wikipedia/commons/c/ca/LinkedIn_logo_initials.png" alt="LinkedIn" /> </a> <a href="https://medium.com/@debrajrout28" target="_blank" class="social-button medium"> <img src="https://upload.wikimedia.org/wikipedia/commons/e/ec/Medium_logo_Monogram.svg" alt="Medium" /> </a> <a href="https://www.instagram.com/_dev1.8/" target="_blank" class="social-button instagram"> <img src="https://upload.wikimedia.org/wikipedia/commons/a/a5/Instagram_icon.png" alt="Instagram" /> </a> </div> <style> .social-links { display: flex; justify-content: center; gap: 15px; margin-top: 10px; } .social-button img { width: 40px; height: 40px; transition: transform 0.3s ease; } .social-button:hover img { transform: scale(1.1); } h1 { font-size: 2.5em; color: #333; } p { font-size: 1.2em; color: #666; } </style>

## FAQ

Here are some frequent questions and their answers.

### 1. How to parse scrape pages?

`dev_scrap` has only a simple request module for making requests. That means you cannot directly parse ajax pages with it, but in general you will have those scenarios:

1.  **The ajax response is in JSON format.** In this case, you can make the request directly, without needing a scraping library.
2.  **The ajax response gives you HTML back.** Instead of calling the main website (e.g. example.com), pass to `dev_scrap` the ajax url (e.g. `example.com/api/that-endpoint`) and you will you will be able to parse the response
3.  **The ajax request is so complicated that you don't want to reverse-engineer it.** In this case, use a headless browser (e.g. Google Chrome, Electron, PhantomJS) to load the content and then use the `.scrapeHTML` method from scrape it once you get the HTML loaded on the page.

### 2. Crawling

There is no fancy way to crawl pages with `dev_scrap`. For simple scenarios, you can parse the list of urls from the initial page and then, using Promises, parse each page. Also, you can use a different crawler to download the website and then use the `.scrapeHTML` method to scrape the local files.

### 3. Local files

Use the `.scrapeHTML` to parse the HTML read from the local files using `fs.readFile`.

## :clipboard: Example

```js
const scrapeIt = require("scrape-it");

// Promise interface
scrapeIt("https://ionicabizau.net", {
  title: ".header h1",
  desc: ".header h2",
  avatar: {
    selector: ".header img",
    attr: "src",
  },
}).then(({ data, status }) => {
  console.log(`Status Code: ${status}`);
  console.log(data);
});

// Async-Await
(async () => {
  const { data } = await scrapeIt("https://ionicabizau.net", {
    // Fetch the articles
    articles: {
      listItem: ".article",
      data: {
        // Get the article date and convert it into a Date object
        createdAt: {
          selector: ".date",
          convert: (x) => new Date(x),
        },

        // Get the title
        title: "a.article-title",

        // Nested list
        tags: {
          listItem: ".tags > span",
        },

        // Get the content
        content: {
          selector: ".article-content",
          how: "html",
        },

        // Get attribute value of root listItem by omitting the selector
        classes: {
          attr: "class",
        },
      },
    },

    // Fetch the blog pages
    pages: {
      listItem: "li.page",
      name: "pages",
      data: {
        title: "a",
        url: {
          selector: "a",
          attr: "href",
        },
      },
    },

    // Fetch some other data from the page
    title: ".header h1",
    desc: ".header h2",
    avatar: {
      selector: ".header img",
      attr: "src",
    },
  });
  console.log(data);
})();
```

## :question: Get Help

There are few ways to get help:

1.  Please [post questions on Stack Overflow](https://stackoverflow.com/questions/ask). You can open issues with questions, as long you add a link to your Stack Overflow question.
2.  For bug reports and feature requests, open issues. :bug:
3.  For direct and quick help, you can [use Codementor](https://www.codementor.io/johnnyb). :rocket:

## :memo: Documentation

### `dev_scrap(url, opts, cb)`

A scraping module for humans.

#### Params

- **String|Object** `url`: The page url or request options.
- **Object** `opts`: The options passed to `scrapeHTML` method.
- **Function** `cb`: The callback function.

#### Return

- **Promise** A promise object resolving with:
  - `data` (Object): The scraped data.
  - `$` (Function): The Cheeerio function. This may be handy to do some other manipulation on the DOM, if needed.
  - `response` (Object): The response object.
  - `body` (String): The raw body as a string.

### `scrapeIt.scrapeHTML($, opts)`

Scrapes the data in the provided element.

#### Params

- **Cheerio** `$`: The input element.
- **Object** `opts`: An object containing the scraping information.
  If you want to scrape a list, you have to use the `listItem` selector:

  - `listItem` (String): The list item selector.
  - `data` (Object): The fields to include in the list objects:
    - `<fieldName>` (Object|String): The selector or an object containing:
      - `selector` (String): The selector.
      - `convert` (Function): An optional function to change the value.
      - `how` (Function|String): A function or function name to access the
        value.
      - `attr` (String): If provided, the value will be taken based on
        the attribute name.
      - `trim` (Boolean): If `false`, the value will _not_ be trimmed
        (default: `true`).
      - `closest` (String): If provided, returns the first ancestor of
        the given element.
      - `eq` (Number): If provided, it will select the _nth_ element.
      - `texteq` (Number): If provided, it will select the _nth_ direct text child.
        Deep text child selection is not possible yet.
        Overwrites the `how` key.
      - `listItem` (Object): An object, keeping the recursive schema of
        the `listItem` object. This can be used to create nested lists.

  **Example**:

  ```js
  {
     articles: {
         listItem: ".article"
       , data: {
             createdAt: {
                 selector: ".date"
               , convert: x => new Date(x)
             }
           , title: "a.article-title"
           , tags: {
                 listItem: ".tags > span"
             }
           , content: {
                 selector: ".article-content"
               , how: "html"
             }
           , traverseOtherNode: {
                 selector: ".upperNode"
               , closest: "div"
               , convert: x => x.length
             }
         }
     }
  }
  ```

  If you want to collect specific data from the page, just use the same
  schema used for the `data` field.

  **Example**:

  ```js
  {
       title: ".header h1"
     , desc: ".header h2"
     , avatar: {
           selector: ".header img"
         , attr: "src"
       }
  }
  ```

#### Return

- **Object** The scraped data.

Thanks! :heart:
