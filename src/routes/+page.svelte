<script>
  import { page } from '$app/stores';
  import { onMount } from 'svelte';
  // import { browser } from '$app/environment';

  import { Telegram, Facebook, X, Line } from 'svelte-share-buttons-component';

  import {
    PUBLIC_CAMPAIGN_LINK,
    PUBLIC_CANONICAL_ORIGIN,
    PUBLIC_SITE_DESCRIPTION,
    PUBLIC_SITE_NAME
  } from '$env/static/public';
  import ThankyouCard from './ThankyouCard.svelte';

  import { loadViewerData, rasterize } from '$lib/utils';

  /** @type {import('./$types').PageData} */
  export let data;

  let userName = data?.searchParams?.userName?.trim()?.replace(/^@+/, '');
  let thankYouText = (data?.searchParams?.text || '').substring(0, 50);

  let alsoKnownAs = (data?.searchParams?.aka || '').substring(0, 17);
  let showQrCode = data?.searchParams?.userName ? 'showQrCode' in data?.searchParams : true;
  $: displayName = (data?.data?.user?.displayName ?? data?.data?.displayName)?.trim() || '';

  let dataSvgEl;
  async function downloadAsPng(event) {
    // console.log('event is:', event, dataSvgEl, data);
    // const blob = await rasterize(dataSvgEl, { scale: 1.25, width: 1200, height: 754 });
    const blob = await rasterize(dataSvgEl, { scale: 2, width: 2000, height: 2000 });
    // console.log(`got blob:`, blob);
    const blobUrl = URL.createObjectURL(blob);
    // console.log('got blobUrl:', blobUrl);
    const link = document.createElement('a');
    link.download = `${displayName ?? 'untitled'}.png`;
    link.href = blobUrl;
    document.body.appendChild(link);
    link.click();
    // always revoke, avoid leaking
    URL.revokeObjectURL(blobUrl);
    document.body.removeChild(link);
  }

  $: updatedTitle = PUBLIC_SITE_NAME + (displayName ? ` - ${displayName.trim()}` : '');
  $: shareUrl = `${PUBLIC_CANONICAL_ORIGIN}/${$page.url.search}`;

  let viewerData;
  onMount(() => {
    (async function () {
      if (!userName) {
        viewerData = await loadViewerData();
        // console.log('no input, fetch viewer data:', viewerData);
        userName = viewerData?.data?.user?.userName || '';
      }
    })();
  });
</script>

<svelte:head>
  <title>{updatedTitle}</title>
  <meta name="description" content={PUBLIC_SITE_DESCRIPTION} />
  <meta name="keywords" content={'matters,identity'} />
  <meta property="og:title" content={updatedTitle} />
  <meta property="og:description" content={PUBLIC_SITE_DESCRIPTION} />
  <meta property="og:type" content="website" />
  <meta property="og:image" content={`${PUBLIC_CANONICAL_ORIGIN}/img/screenshot.png`} />
  <meta property="og:url" content={shareUrl} />
  <meta property="twitter:title" content={updatedTitle} />
  <meta property="twitter:description" content={PUBLIC_SITE_DESCRIPTION} />
  <meta property="twitter:card" content="summary_large_image" />
  <meta property="twitter:image" content={`${PUBLIC_CANONICAL_ORIGIN}/img/screenshot.png`} />
  <meta property="twitter:url" content={shareUrl} />
  <meta property="meta:userDescription" content={`${data?.user?.info?.description}`} />
  <link rel="canonical" href={shareUrl} />
</svelte:head>

<header>
  <h1>{PUBLIC_SITE_DESCRIPTION}</h1>
  {#if !data?.searchParams?.userName}
    <p>
      在第一行輸入你的 Matters ID，也就是個人主頁網址 @ 後面那一串（舉例：Matty 的 ID 是 hi176）；<br
      />第二行可以自由輸入你的年末致謝詞（需 50 字內）；<br />第三行輸入你想顯示的暱稱（<b
        >填寫後會在卡片正上方顯示喔！</b
      >若不想顯示任何暱稱就不用填寫～）<br />——都輸入完後按下 enter 就可以囉 💌
    </p>
  {:else}
    <p>
      感謝 2023
      年的自己，希望這句致謝詞可以讓我們與過去一年的自己擊掌、對話、擁抱——那樣會更看清來時的路。<br
      />再回身，2024廣闊而舒展。
    </p>
    <div class="tools-group">
      <X
        class="share-button"
        text={updatedTitle}
        url={shareUrl}
        hashtags="2023馬特市創作成就"
        via="MattersLab"
        related="MattersLab"
      />
      <Facebook class="share-button" quote={updatedTitle} url={shareUrl} />
      <Telegram class="share-button" text={updatedTitle} url={shareUrl} />
      <Line class="share-button" url={shareUrl} />
      <a href="https://Matters.Town" target="_blank" rel="noreferrer" class="share-link"
        ><div>回到 Matters</div></a
      >
    </div>
  {/if}
</header>

<section>
  <div id="frame">
    {#if data?.searchParams?.userName}
      <ThankyouCard userData={data} {thankYouText} {alsoKnownAs} {showQrCode} bind:el={dataSvgEl} />
    {:else}
      <form data-sveltekit-reload>
        <div class="row userNameContainer">
          <!-- <label for="userName">用戶名 (@userName):</label> -->
          <input
            type="text"
            name="userName"
            id="userName"
            size={20}
            maxlength={20}
            placeholder="輸入你的 Matters ID"
            pattern="[A-z0-9À-ž]+"
            min-length="2"
            bind:value={userName}
          />
        </div>
        <div class="row">
          <textarea
            rows={3}
            maxlength={50}
            name="text"
            placeholder="致謝詞（50字內）"
            bind:value={thankYouText}
          />
        </div>
        <div class="row">
          <!-- <label for="alsoKnownAs">Also Known As:</label> -->
          <input
            type="text"
            name="aka"
            id="alsoKnownAs"
            size={17}
            maxlength={17}
            placeholder="自由輸入暱稱、綽號、頭銜等，也可以留白"
            bind:value={alsoKnownAs}
          />
        </div>
        <div class="row showQrCode">
          <label>
            <input type="checkbox" name="showQrCode" bind:checked={showQrCode} />
            顯示 QRcode
          </label>
        </div>
        <div class="row flex-1">
          <button type="submit" class="btn" disabled={!thankYouText || !userName}>Enter</button>
        </div>
      </form>
    {/if}
  </div>
</section>
{#if data?.searchParams?.userName}
  <div class="row flex-1">
    <div class="download-links">
      <button class="btn" on:click={downloadAsPng}>下載截圖</button>
    </div>
  </div>
{/if}

<style>
  header {
    position: relative;
    min-height: 20rem;
  }
  h1 {
    margin: 1rem;
    padding: 2rem 0;
  }
  :global(body) {
    font-family:
      Noto Sans CJK TC,
      PingFang TC,
      -apple-system,
      BlinkMacSystemFont,
      Segoe UI,
      Roboto,
      Helvetica,
      Arial,
      Hiragino Sans GB,
      Heiti TC,
      Microsoft JhengHei,
      Microsoft YaHei UI,
      Microsoft YaHei,
      sans-serif;
    letter-spacing: 0em;
  }

  section {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    flex: 0.6;
  }

  div#frame {
    display: flex;
    align-items: center;
    justify-content: center;

    min-height: 15rem;
    width: 100%;
    /* background: teal; */
  }
  div#frame > :global(svg) {
    font-family:
      Jf jinxuan,
      Noto Serif TC,
      Noto Serif SC,
      Noto Serif,
      Songti TC,
      Songti SC,
      PingFang TC,
      apple-system,
      BlinkMacSystemFont,
      Segoe UI,
      Roboto,
      Helvetica,
      Arial,
      Hiragino Sans GB,
      Heiti TC,
      Microsoft JhengHei,
      Microsoft YaHei UI,
      Microsoft YaHei,
      sans-serif;
  }

  .row {
    display: flex;
    align-items: baseline;
    justify-content: center;
    margin: 0.5rem 0;
  }

  form {
    width: 24rem;
    flex-direction: row;
    justify-content: space-evenly;
  }

  form .row input[type='text'],
  form .row textarea {
    font-size: 1.1rem;
    border: 1px solid #ccc;
    padding: 0.5rem 1rem;

    border: 1px solid black;
    border-radius: 0.5rem;

    display: block;
    width: 100%;
  }
  form .row input:focus,
  form .row textarea:focus {
    outline: none !important;
    border: 1px solid red;
    box-shadow: 0 0 10px #278e7b;
  }
  form .row.userNameContainer {
    position: relative;
  }
  form .row.userNameContainer:before {
    position: absolute;
    left: 1rem;
    color: grey;
    content: '@';
    line-height: 50px;
  }
  form .row.userNameContainer input#userName {
    padding-left: 2rem;
    line-height: 2rem;
  }
  form .row.showQrCode {
    justify-content: left;
  }

  .flex-1 {
    flex: 0 0 100%;
  }

  button.btn {
    border: 1px solid black;
    border-radius: 0.5rem;
    box-sizing: content-box;
    background-color: #278e7b;
    color: black;
    padding: 0.5rem 1rem;
    font-size: 16px;
    cursor: pointer;
    border-color: #ccc;
    color: white;
  }
  button.btn:hover:enabled {
    background-color: #278e7b;
    color: white;
  }
  button.btn:disabled,
  button.btn[disabled] {
    border-color: #999999;
    background-color: #cccccc;
  }

  .download-links {
    margin-bottom: 1rem;
  }

  .tools-group {
    position: absolute;
    right: 0;
    bottom: 0.25rem;
    margin: 0.5rem 0;
    padding-top: 2rem;
    display: flex;
    align-items: center;
    justify-content: flex-end;
  }
  .tools-group > :global(* + *) {
    margin-left: 0.5rem;
  }
  .tools-group :global(.share-button) {
    margin-left: 0.5rem;
    border-radius: 0.5rem;
  }
  .tools-group a.share-link {
    margin-left: 2rem;
    box-sizing: border-box;
    border: 1px dotted #grey;
  }

  @media (min-width: 768px) {
    div#frame > :global(svg) {
      width: 500px;
      height: 500px;
    }

    header {
      min-height: 16rem;
    }
    form .row {
      justify-content: center;
    }
  }
</style>