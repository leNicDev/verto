<script lang="typescript">
  import NavBar from "../components/NavBar.svelte";
  import Hero from "../components/home/Hero.svelte";
  // import Volume from "../components/home/Volume.svelte";
  import LatestTrades from "../components/home/LatestTrades.svelte";
  import About from "../components/home/About.svelte";
  import Footer from "../components/Footer.svelte";
  import { keyfile, loggedIn } from "../stores/keyfileStore";
  import { goto } from "@sapper/app";

  if (process.browser && $loggedIn) goto("/app");
</script>

<svelte:head>
  <title>Verto</title>
  <meta
    name="description"
    content="Token Exchange System for Arweave Profit-Sharing Tokens" />
  <meta content="Verto" property="og:title" />
  <meta
    content="Token Exchange System for Arweave Profit-Sharing Tokens"
    property="og:description" />
  <!--<meta property="og:image" content="https://image">
  <meta name="twitter:card" content="summary_large_image">-->
  <meta name="twitter:title" content="Verto" />
  <meta
    name="twitter:description"
    content="Token Exchange System for Arweave Profit-Sharing Tokens" />
  <!--<meta name="twitter:image" content="https://iimage">-->
</svelte:head>

<NavBar hero={true} />
<Hero />
<LatestTrades />
<!-- <Volume /> -->
<About id="read-more">
  <h1 class="title">What are PSTs?</h1>
  <p class="about-description">
    Profit-Sharing Tokens, or PSTs, are a new incentivization mechanism for the
    open web that allow developers to earn a stream of micro-dividends for the
    duration their application is used. (<a
      href="https://medium.com/@arweave/profit-sharing-tokens-a-new-incentivization-mechanism-for-an-open-web-1f2532411d6e">Source</a>)
  </p>
  <p class="about-description">
    In order for PSTs to have value, however, they need to be able to be
    exchanged for other PSTs or AR. This is where Verto comes in...
  </p>
</About>
<About>
  <h1 class="title">Why Verto?</h1>
  <p class="about-description">
    Verto is a completely decentralized network of trading posts built on top of
    the blockweave. Anyone can host their own trading post and power the
    exchange, while also being incentivized to do so. With Verto, you can pick
    the trading post you'd like to use and exchange your PSTs freely!
  </p>
  <p class="about-description">
    Decisions for Verto are made by our very own Profit-Sharing DAO, which means
    that anyone can have a say in the direction of our platform.
  </p>
  <p class="about-description">
    You can also easily host your own Trading Post, while accruing PSTs for
    doing so. See our <a href="https://github.com/useverto/trading-post">Trading
      Post Repository</a> for documentation.
  </p>
</About>
<About>
  <h1 class="title">VRT - The Verto Protocol Token</h1>
  <p class="about-description">
    VRT is a way for any person to passively earn a volume-weighted index of all
    profit sharing tokens being traded on the exchange. 0.5% of any PST
    transaction made on the exchange is sent straight to a VRT holder.
  </p>
  <p class="about-description">
    By holding VRT, a user is also inherently a member of the Verto
    Profit-Sharing DAO, which means they can choose to stake their tokens to
    have a say in the various decisions made for the platform.
  </p>
  <p class="about-description">
    <li>
      VRT Contract Address: <a href="https://community.xyz/#usjm4PCxUd5mtaon7zc97-dt-3qf67yPyqgzLnLqk5A/tokens">usjm4PCxUd5mtaon7zc97-dt-3qf67yPyqgzLnLqk5A</a>
    </li>
    <li>Initial Supply: 250,000,000</li>
  </p>
</About>
<About>
  <h1 class="title">Get in Touch</h1>
  <p class="about-description">
    Have a question or are interested in purchasing more VRT than you can find
    on the exchange? Join our <a href="https://discord.gg/sNgJkMg">Discord</a>.
    Let's chat!
  </p>
</About>
<Footer scrollTransition={true} />
