<script lang="typescript">
  import { address, loggedIn } from "../../stores/keyfileStore";
  import NavBar from "../../components/NavBar.svelte";
  import Footer from "../../components/Footer.svelte";
  import Loading from "../../components/Loading.svelte";
  import SkeletonLoading from "../../components/SkeletonLoading.svelte";
  import moment from "moment";
  import { goto } from "@sapper/app";
  import { fade } from "svelte/transition";
  import { onMount } from "svelte";
  import { query } from "../../api-client";
  import Arweave from "arweave";
  import allTransactionsQuery from "../../queries/allTransactions.gql";
  import type { Transaction } from "../../utils/types";

  if (process.browser && !$loggedIn) goto("/");

  let client, element;
  let transactions: Transaction[] = [];
  let lastCursorOut = "",
    lastCursorIn = "";
  let hasNextOut = true,
    hasNextIn = true,
    loadedTransactions = false,
    loading = false;
  let y: number, windowHeight: number;

  onMount(() => loadMoreTransactions());

  async function loadMoreTransactions() {
    if (!process.browser) return [];

    loading = true;

    const client = new Arweave({
      host: "arweave.net",
      port: 443,
      protocol: "https",
      timeout: 20000,
    });

    const outQuery = hasNextOut
        ? (
            await query({
              query: allTransactionsQuery,
              variables: {
                owners: [$address],
                recipients: null,
                after: lastCursorOut,
              },
            })
          ).data
        : null,
      inQuery = hasNextIn
        ? (
            await query({
              query: allTransactionsQuery,
              variables: {
                recipients: [$address],
                owners: null,
                after: lastCursorIn,
              },
            })
          ).data
        : null;

    const outTxs = hasNextOut ? outQuery.transactions.edges : null,
      inTxs = hasNextIn ? inQuery.transactions.edges : null;

    hasNextOut = hasNextOut
      ? outQuery.transactions.pageInfo.hasNextPage
      : false;
    hasNextIn = hasNextIn ? inQuery.transactions.pageInfo.hasNextPage : false;

    let _transactions: Transaction[] = [];

    if (outTxs !== null)
      outTxs.map(({ node, cursor }) => {
        lastCursorOut = cursor;
        _transactions.push({
          id: node.id,
          amount: parseFloat(node.quantity.ar),
          type: "out",
          status: "",
          timestamp: node.block.timestamp,
        });
      });

    if (inTxs !== null)
      inTxs.map(({ node, cursor }) => {
        lastCursorIn = cursor;
        _transactions.push({
          id: node.id,
          amount: parseFloat(node.quantity.ar),
          type: "in",
          status: "",
          timestamp: node.block.timestamp,
        });
      });

    if (_transactions.length > 0)
      _transactions.sort((a, b) => b.timestamp - a.timestamp);

    for (let i = 0; i < _transactions.length; i++) {
      try {
        let res = await client.transactions.getStatus(_transactions[i].id);
        if (res.status === 200) _transactions[i].status = "success";
        else _transactions[i].status = "pending";
      } catch (error) {
        console.log(error);
      }
    }

    transactions = transactions.concat(_transactions);
    loadedTransactions = true;
    setTimeout(() => (loading = false), 400); // wait for animation and latency to complete (needed for the scroll)
  }

  $: {
    if (element !== undefined) {
      let componentY = element.offsetTop + element.offsetHeight;
      if (
        componentY <= y + windowHeight + 120 &&
        loadedTransactions &&
        !loading &&
        (hasNextOut || hasNextIn)
      )
        loadMoreTransactions();
    }
  }
</script>

<svelte:head>
  <title>Transactions overview</title>
</svelte:head>

<svelte:window bind:scrollY={y} bind:innerHeight={windowHeight} />
<NavBar />
<div class="all-transactions" in:fade={{ duration: 300 }}>
  <h1 class="title">All Transactions</h1>
  <table>
    <tr>
      <th style="text-transform: none">TxID</th>
      <th>Amount</th>
    </tr>
    {#if !loadedTransactions}
      {#each Array(10) as _}
        <tr>
          <td style="width: 70%">
            <SkeletonLoading style="width: 100%; height: 1.5em;" />
          </td>
          <td style="width: 20%">
            <SkeletonLoading style="width: 100%; height: 1.5em;" />
          </td>
        </tr>
      {/each}
    {:else}
      {#each transactions as tx}
        <tr in:fade={{ duration: 150 }}>
          <td style="width: 70%">
            <a href="https://viewblock.io/arweave/tx/{tx.id}">
              <span class="direction">{tx.type}</span>
              {tx.id}
            </a>
            <span class="status {tx.status}" />
          </td>
          <td style="width: 20%">{tx.amount} AR</td>
        </tr>
      {/each}
      {#if loading}
        <Loading style="position: absolute; left: 50%;" />
        <br />
      {/if}
    {/if}
  </table>
</div>
<Footer />
<span style="width: 100%; height: 1px" bind:this={element} />

<style lang="sass">

  @import "../../styles/tables.sass"
  @import "../../styles/general.sass"

  .all-transactions
    @include page
    @include table 

    p
      color: var(--secondary-text-color)

    td
      a
        text-decoration: none
        color: var(--primary-text-color)

</style>
