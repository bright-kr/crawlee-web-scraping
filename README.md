# Crawlee를 사용한 Web스크레이핑

[![Promo](https://github.com/bright-kr/LinkedIn-Scraper/raw/main/Proxies%20and%20scrapers%20GitHub%20bonus%20banner.png)](https://brightdata.co.kr/) 

Crawlee를 사용하여 효율적인 [Node.js로 Web스크레이핑](https://brightdata.co.kr/blog/how-tos/web-scraping-with-node-js)을 수행하는 방법을 알아보겠습니다:

- [Crawlee로 기본 Web스크레이핑](#basic-web-scraping-with-crawlee)
- [Crawlee로 프록시 로ーテ이션](#proxy-rotation-with-crawlee)
- [Crawlee로 세션 관리](#sessions-management-with-crawlee)
- [Crawlee로 동적 콘텐츠 처리](#dynamic-content-handling-with-crawlee)

## Prerequisites

시작하기 전에 다음 Prerequisites가 설치되어 있는지 확인합니다:

* **[Node.js](https://nodejs.org/).**
* **[npm](https://www.npmjs.com/):** 일반적으로 Node.js와 함께 제공됩니다. 터미널에서 `node -v` 또는 `npm -v` 를 실행하여 설치를 확인할 수 있습니다.
* **원하는 코드 에디터:** 이 튜토리얼에서는 [Visual Studio Code](https://code.visualstudio.com/)를 사용합니다.

## Basic Web Scraping with Crawlee

먼저 [Books to Scrape](https://books.toscrape.com/) 웹사이트를 스크레이핑해 보겠습니다.

터미널 또는 셸을 열고 Node.js 프로젝트를 초기화합니다:

```bash
mkdir crawlee-tutorial
cd crawlee-tutorial
npm init -y
```

Crawlee 라이브러리를 설치합니다:

```bash
npm install crawlee
```

데이터를 효과적으로 스크레이핑하려면 대상 웹사이트의 HTML 구조를 점검합니다. 브라우저에서 사이트를 열고 페이지의 아무 곳이나 우클릭한 다음 **Developer Tools**에서 **Inspect** 또는 **Inspect Element**를 선택합니다.

![Inspect HTML element](https://github.com/bright-kr/crawlee-web-scraping/blob/main/images/Inspect-HTML-element-1024x540.png)

**Developer Tools**의 **Elements** 탭에는 페이지의 HTML 레이아웃이 표시됩니다. 이 예시에서는:  

- 각 책은 class가 `product_pod`인 `article` 태그 안에 있습니다.  
- 책 제목은 `h3` 태그에 있으며, 실제 제목은 중첩된 `a` 태그의 `title` 속성에 저장됩니다.  
- 책 가격은 class가 `price_color`인 `p` 태그 안에 있습니다.  

![Inspect the HTML elements on the Books to Scrape website](https://github.com/bright-kr/crawlee-web-scraping/blob/main/images/Inspect-the-HTML-elements-on-the-Books-to-Scrape-website-1024x522.png)

프로젝트의 루트 디렉터리 아래에 `scrape.js` 라는 파일을 생성하고 다음 코드를 추가합니다:

```js
const { CheerioCrawler } = require('crawlee');

const crawler = new CheerioCrawler({
    async requestHandler({ request, $ }) {
        const books = [];
        $('article.product_pod').each((index, element) => {
            const title = $(element).find('h3 a').attr('title');
            const price = $(element).find('.price_color').text();
            books.push({ title, price });
        });
        console.log(books);
    },
});

crawler.run(['https://books.toscrape.com/']);
```

이 코드는 `crawlee`의 `CheerioCrawler`를 사용하여 `https://books.toscrape.com/`에서 책 제목과 가격을 추출합니다. HTML을 가져온 다음 jQuery와 유사한 문법으로 `<article class="product_pod">` 요소를 선택하고, 결과를 콘솔에 출력합니다.

`scrape.js` 파일에 코드를 추가한 후 다음 명령으로 실행합니다:

```bash
node scrape.js
```

터미널에 책 제목과 가격 배열이 출력되어야 합니다:

```
…output omitted…
  {
    title: 'The Boys in the Boat: Nine Americans and Their Epic Quest for Gold at the 1936 Berlin Olympics',
    price: '£22.60'
  },
  { title: 'The Black Maria', price: '£52.15' },
  {
    title: 'Starving Hearts (Triangular Trade Trilogy, #1)',
    price: '£13.99'
  },
  { title: "Shakespeare's Sonnets", price: '£20.66' },
  { title: 'Set Me Free', price: '£17.46' },
  {
    title: "Scott Pilgrim's Precious Little Life (Scott Pilgrim #1)",
    price: '£52.29'
  },
…output omitted…
```

## Proxy Rotation with Crawlee

프록시는 컴퓨터와 인터넷 사이에서 중간자 역할을 하며, Web 리クエスト를 전달하는 동시에 IP 주소를 마스킹합니다. 이는 속도 제한 및 IP 차단을 방지하는 데 도움이 됩니다.

Crawlee는 재시도, 오류, 로ーテーティング프록시에 대한 내장 처리 기능을 제공하여 프록시 구현을 단순화합니다.

다음으로 프록시를 설정하고, 유효한 프록시 주소를 얻은 뒤, 리クエスト가 해당 프록시를 통해 라우팅되는지 확인합니다.

무료 프록시는 종종 느리고, 안전하지 않으며, 민감한 웹 작업에 대해 신뢰성이 떨어지므로, 보안적이고 안정적이며 신뢰할 수 있는 프록시를 제공하는 Bright Data와 같은 신뢰 가능한 서비스를 사용하는 것을 고려하시기 바랍니다. 또한 무료 체험을 제공하므로, 가입 전에 서비스를 테스트할 수 있습니다. 

Bright Data를 사용하려면 [홈페이지](https://brightdata.co.kr/)에서 **Start free trial** 버튼을 클릭하고 필수 정보를 입력하여 계정을 생성합니다.

계정 생성 후 Bright Data 대시보드에 로그인하고 **Proxies & Scraping Infrastructure**로 이동한 다음 **[Residential Proxies](/proxy-types/residential-proxies)**를 선택하여 새 프록시를 추가합니다:

![Add a residential proxy](https://github.com/bright-kr/crawlee-web-scraping/blob/main/images/Add-a-residential-proxy-1024x574.png)

기본 설정을 유지한 채로 **Add**를 클릭하여 レジデンシャル프록시 생성을 완료합니다.

인증서 설치를 요청받으면 **Proceed without certificate**를 선택할 수 있습니다. 다만, 프로덕션 및 실제 사용 사례에서는 프록시 정보가 노출될 경우 오용을 방지하기 위해 인증서를 설정해야 합니다.

생성이 완료되면 host, port, username, password를 포함한 프록시 자격 증명을 기록해 두십시오. 다음 단계에서 필요합니다:

![Bright Data proxy credentials](https://github.com/bright-kr/crawlee-web-scraping/blob/main/images/Bright-Data-proxy-credentials-1024x557.png)

프로젝트의 루트 디렉터리에서 다음 명령을 실행하여 [axios](https://www.npmjs.com/package/axios) 라이브러리를 설치합니다:

```bash
npm install axios
```

`axios` 라이브러리는 `http://lumtest.com/myip.json`로 GET 리クエ스트를 보내는 데 사용되며, 이 URL은 사용 중인 프록시에 대한 세부 정보를 반환합니다.

이를 구현하려면 프로젝트 루트 디렉터리에 `scrapeWithProxy.js` 파일을 만들고 다음 코드를 추가합니다:

```js
const { CheerioCrawler } = require("crawlee");
const { ProxyConfiguration } = require("crawlee");
const axios = require("axios");

const proxyConfiguration = new ProxyConfiguration({
  proxyUrls: ["http://USERNAME:PASSWORD@HOST:PORT"],
});

const crawler = new CheerioCrawler({
  proxyConfiguration,
  async requestHandler({ request, $, response, proxies }) {
    // Make a GET request to the proxy information URL
    try {
      const proxyInfo = await axios.get("http://lumtest.com/myip.json", {
        proxy: {
          host: "HOST",
          port: PORT,
          auth: {
            username: "USERNAME",
            password: "PASSWORD",
          },
        },
      });
      console.log("Proxy Information:", proxyInfo.data);
    } catch (error) {
      console.error("Error fetching proxy information:", error.message);
    }

    const books = [];
    $("article.product_pod").each((index, element) => {
      const title = $(element).find("h3 a").attr("title");
      const price = $(element).find(".price_color").text();
      books.push({ title, price });
    });
    console.log(books);
  },
});

crawler.run(["https://books.toscrape.com/"]);
```

> **Note:**
> 
> `HOST`, `PORT`, `USERNAME`, `PASSWORD`를 사용 중인 자격 증명으로 반드시 교체하십시오.

이 코드는 `crawlee`의 `CheerioCrawler`를 사용하여 지정된 프록시를 통해 리クエスト를 라우팅하면서 `https://books.toscrape.com/`에서 데이터를 스크레이핑합니다.  

- 프록시는 `ProxyConfiguration`으로 구성합니다.  
- `http://lumtest.com/myip.json`로 GET 리クエ스트를 보내 프록시 세부 정보를 가져와 콘솔에 출력합니다.  
- Cheerio의 jQuery 유사 문법을 사용하여 책 제목과 가격을 추출하고 콘솔에 출력합니다.  

코드를 실행하여 프록시 설정을 테스트하고 정상 동작을 확인합니다:

```bash
node scrapeWithProxy.js
```

이전과 유사한 결과가 표시되지만, 이번에는 리クエスト가 Bright Data 프록시를 통해 라우팅됩니다. 또한 콘솔에 프록시의 세부 정보가 출력되는 것을 확인할 수 있습니다:

```js
Proxy Information: {
  country: 'US',
  asn: { asnum: 21928, org_name: 'T-MOBILE-AS21928' },
  geo: {
    city: 'El Paso',
    region: 'TX',
    region_name: 'Texas',
    postal_code: '79925',
    latitude: 31.7899,
    longitude: -106.3658,
    tz: 'America/Denver',
    lum_city: 'elpaso',
    lum_region: 'tx'
  }
}
[
  { title: 'A Light in the Attic', price: '£51.77' },
  { title: 'Tipping the Velvet', price: '£53.74' },
  { title: 'Soumission', price: '£50.10' },
  { title: 'Sharp Objects', price: '£47.82' },
  { title: 'Sapiens: A Brief History of Humankind', price: '£54.23' },
  { title: 'The Requiem Red', price: '£22.65' },
…output omitted..
```

`node scrapingWithBrightData.js`로 스크립트를 실행하면 매번 다른 IP 주소가 표시되어 Bright Data가 위치와 IP를 자동으로 로ーテ이션한다는 것을 확인할 수 있습니다. 이러한 로ーテ이션은 웹사이트를 스크레이핑할 때 차단 및 IP 밴을 방지하는 데 도움이 됩니다.

> **Note:**
> 
> `proxyConfiguration`에서 서로 다른 프록시 IP를 전달할 수도 있지만, Bright Data가 이를 대신 처리하므로 IP를 별도로 지정할 필요가 없습니다.

## Sessions Management with Crawlee

세션은 특히 Cookie 또는 로그인 세션을 사용하는 사이트에서 여러 리クエ스트 간 상태를 유지하는 데 도움이 됩니다.  

세션 관리를 구현하려면 프로젝트 루트 디렉터리에 `scrapeWithSessions.js` 파일을 만들고 다음 코드를 추가합니다:

```js
const { CheerioCrawler, SessionPool } = require("crawlee");

(async () => {
  // Open a session pool
  const sessionPool = await SessionPool.open();

  // Ensure there is a session in the pool
  let session = await sessionPool.getSession();
  if (!session) {
    session = await sessionPool.createSession();
  }

  const crawler = new CheerioCrawler({
    useSessionPool: true, // Enable session pool
    async requestHandler({ request, $, response, session }) {
      // Log the session information
      console.log(`Using session: ${session.id}`);

      // Extract book data and log it (for demonstration)
      const books = [];
      $("article.product_pod").each((index, element) => {
        const title = $(element).find("h3 a").attr("title");
        const price = $(element).find(".price_color").text();
        books.push({ title, price });
      });
      console.log(books);
    },
  });

  // First run
  await crawler.run(["https://books.toscrape.com/"]);
  console.log("First run completed.");

  // Second run
  await crawler.run(["https://books.toscrape.com/"]);
  console.log("Second run completed.");
})();
```

이 코드는 `crawlee`의 `CheerioCrawler`와 `SessionPool`을 사용하여 `https://books.toscrape.com/`에서 데이터를 스크레이핑합니다.  

- 세션 풀을 초기화하고 크롤러에 할당합니다.  
- `requestHandler`는 세션 세부 정보를 로깅하고 Cheerio 셀렉터를 사용해 책 제목과 가격을 추출합니다.  
- 스크립트는 두 번 연속으로 스크레이핑을 실행하며, 매번 세션 ID를 로깅합니다.  

코드를 실행하여 서로 다른 세션이 사용되는지 확인합니다.

```bash
node scrapeWithSessions.js
```

이전과 유사한 결과가 출력되지만, 이번에는 각 실행에 대한 세션 ID가 함께 표시됩니다:

```
Using session: session_GmKuZ2TnVX
[
  { title: 'A Light in the Attic', price: '£51.77' },
  { title: 'Tipping the Velvet', price: '£53.74' },
…output omitted…
Using session: session_lNRxE89hXu
[
  { title: 'A Light in the Attic', price: '£51.77' },
  { title: 'Tipping the Velvet', price: '£53.74' },
…output omitted…
```

코드를 다시 실행하면 다른 세션 ID가 사용되는 것을 확인할 수 있습니다.

## Dynamic Content Handling with Crawlee

**동적 웹사이트**(JavaScript로 콘텐츠를 로드하는 사이트)를 스크레이핑하는 것은 렌더링 이후에만 데이터가 उपलब्ध하므로 어려울 수 있습니다.  

이를 처리하기 위해 Crawlee는 JavaScript를 렌더링하고 사람이 웹페이지를 사용하는 것처럼 상호작용하는 헤드리스 브라우저인 [Puppeteer](https://pptr.dev/)와 통합됩니다.  

데모를 위해 [이 YouTube 페이지](https://www.youtube.com/watch?v=wZ6cST5pexo)에서 콘텐츠를 스크레이핑하겠습니다. **스크레이핑을 수행하기 전에 항상 사이트의 규칙과 서비스 약관을 검토하십시오.**  

약관을 검토한 후, 프로젝트 루트 디렉터리에 `scrapeDynamicContent.js` 파일을 만들고 다음 코드를 추가합니다:

```js
const { PuppeteerCrawler } = require("crawlee");

async function scrapeYouTube() {
  const crawler = new PuppeteerCrawler({
    async requestHandler({ page, request, enqueueLinks, log }) {
      const { url } = request;
      await page.goto(url, { waitUntil: "networkidle2" });

      // Scraping first 10 comments
      const comments = await page.evaluate(() => {
        return Array.from(document.querySelectorAll("#comments #content-text"))
          .slice(0, 10)
          .map((el) => el.innerText);
      });

      log.info(`Comments: ${comments.join("\n")}`);
    },

    launchContext: {
      launchOptions: {
        headless: true,
      },
    },
  });

  // Add the URL of the YouTube video you want to scrape
  await crawler.run(["https://www.youtube.com/watch?v=wZ6cST5pexo"]);
}

scrapeYouTube();
```

그런 다음 다음 명령으로 코드를 실행합니다:

```bash
node scrapeDynamicContent.js
```

이 코드는 Crawlee의 `PuppeteerCrawler`를 사용하여 YouTube 동영상의 댓글을 스크레이핑합니다.  

- 크롤러는 특정 YouTube 동영상 URL로 이동하고 페이지가 완전히 로드될 때까지 대기합니다.  
- CSS 셀렉터 `#comments #content-text`를 사용하여 처음 10개의 댓글을 선택합니다.  
- 추출된 댓글을 콘솔에 로깅합니다.  

실행하면 스크립트는 선택한 동영상의 처음 10개 댓글을 출력합니다.

```
INFO  PuppeteerCrawler: Starting the crawler.
INFO  PuppeteerCrawler: Comments: Who are you rooting for?? US Marines or Ex Cons 
Bro Mateo is a beast, no lifting straps, close stance.
ex convict doing the pushups is a monster.
I love how quick this video was, without nonsense talk and long intros or outros
"They Both have combat experience" is wicked 
That military guy doing that deadlift is really no joke.. ...
One lives to fight and the other fights to live.
Finally something that would test the real outcome on which narrative is true on movies
I like the comradery between all of them. Especially on the Bench Press ... Both team members quickly helped out on the spotting to protect from injury. Well done.
I like this style, no youtube funny business. Just straight to the lifts
…output omitted…
```

이 튜토리얼에서 사용된 모든 코드는 [GitHub](https://github.com/See4Devs/crawlee-web-scraping)에서 확인할 수 있습니다.

## Conclusion

Crawlee는 Web스크레이핑 프로젝트의 효율성과 신뢰성을 향상시키는 데 도움이 될 수 있습니다. 전문 수준의 데이터, 도구, 그리고 프록시로 Web스크레이핑 프로젝트를 한 단계 끌어올릴 준비가 되셨습니까? [즉시 사용 가능한 데이터셋](https://brightdata.co.kr/products/datasets)과 [고급 프록시 서비스](https://brightdata.co.kr/proxy-types)를 제공하여 데이터 수집 노력을 간소화하는 Bright Data의 종합 Web스크레이핑 플랫폼을 살펴보십시오.

지금 가입하고 무료 체험을 시작하십시오!