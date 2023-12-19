<script>
  import { browser } from '$app/environment';
  import QRCode from 'qrcode';

  import {
    defaultImg,
    formatId,
    fromGlobalId,
    loadImageToDataUri,
    loadAvatarDataUri
  } from '$lib/utils';

  import { PUBLIC_CAMPAIGN_LINK } from '$env/static/public';

  export let userData;
  export let el;
  export let aka;
  export let thankYouText;
  export let showQrCode;

  $: {
    console.log('received:', { userData, aka, thankYouText, showQrCode });
  }

  $: displayName =
    (userData?.data?.user?.displayName ?? userData?.data?.displayName)?.trim() || '未知作者';
  $: userName = userData?.data?.user?.userName ?? userData?.data?.userName ?? '';

  $: avatarImg = userData?.data?.user?.avatar ?? userData?.data?.avatar;
  let userImgDataUri, backgroundImgDataUri;
  $: if (browser) {
    if (avatarImg) {
      loadAvatarDataUri(avatarImg).then((dataUri) => {
        userImgDataUri = dataUri;
      });
    }
    loadImageToDataUri(`./thankyou-card-background.jpeg`).then((dataUri) => {
      backgroundImgDataUri = dataUri;
    });
  }

  let mattersQrCodeUri;
  $: if (browser) {
    QRCode.toDataURL(PUBLIC_CAMPAIGN_LINK, { margin: 0 }).then((datauri) => {
      mattersQrCodeUri = datauri;
    });
  }

  $: textMajorFontsize =
    thankYouText.length <= 20
      ? 86
      : thankYouText.length <= 30
        ? 72
        : thankYouText.length <= 40
          ? 62
          : 51;
  $: textMajorLineHeight =
    thankYouText.length <= 20
      ? 120
      : thankYouText.length <= 30
        ? 100
        : thankYouText.length <= 40
          ? 90
          : 80;
</script>

<svg
  viewBox="0 0 1000 1000"
  preserveAspectRatio="xMidYMid meet"
  fill="none"
  xmlns="http://www.w3.org/2000/svg"
  xmlns:xlink="http://www.w3.org/1999/xlink"
  bind:this={el}
  style="--text-major-font-size: {textMajorFontsize}px;
--text-major-line-height: {textMajorLineHeight}px;"
>
  <style>
    p.text,
    text {
      font-family: jf-jinxuan, 'Noto Serif TC', 'Noto Serif SC', 'Noto Serif', 'Songti TC', 'Songti SC', 'PingFang TC', apple-system, 'BlinkMacSystemFont', 'Segoe UI', 'Roboto', 'Helvetica', 'Arial', 'Hiragino Sans GB', 'Heiti TC', 'Microsoft JhengHei', 'Microsoft YaHei UI', 'Microsoft YaHei', sans-serif;
      margin: 0;
      color: white;
    }
    p.text {
      font-size: var(--text-major-font-size);
      line-height: var(--text-major-line-height);
      text-align: left;
      height: 100%;
      font-weight: 500;
      overflow: auto;
    }
  </style>

  {#if aka}
  <style>
    p.text {
      text-align: center;
    }
  </style>
  {/if}


  <rect width="1000" height="1000" fill="url(#pattern0)" />

  <foreignObject x="66" y={(aka ? 261.32 : 243.32) + 40} width="868" height="400">
    <!--
      In the context of SVG embedded in an HTML document, the XHTML
      namespace could be omitted, but it is mandatory in the
      context of an SVG document
    -->
    <p class="text" xmlns="http://www.w3.org/1999/xhtml">
      {thankYouText}
    </p>
  </foreignObject>

  {#if aka}
    <rect width="1000" height="100" fill="#5D2BBD" />
    <rect
      x={530 - 54 * (aka.length / 2) - 58 - (aka.length <= 3 ? 10 : 0)}
      y="21"
      width="58"
      height="58"
      rx="29"
      fill="url(#pattern1)"
      stroke="#FFA4E1"
      stroke-width="2"
    />
    <text
      fill="#FFA4E1"
      xml:space="preserve"
      style="white-space: pre"
      font-size="48"
      font-weight="bold"
      letter-spacing="0.05em"
      text-anchor="middle"><tspan x="530" y="68.24">{aka}</tspan></text
    >
    <text
      fill="#FFDD71"
      xml:space="preserve"
      style="white-space: pre"
      font-size="64"
      font-weight="700"
      letter-spacing="0.05em"
      text-anchor="middle"><tspan x="500" y="261.32">2023年，給自己的致謝詞</tspan></text
    >
  {:else}
    <rect
      x="68"
      y="181"
      width="76"
      height="76"
      rx="38"
      fill="url(#pattern1)"
      stroke="#FFDD71"
      stroke-width="4"
    />
    <text
      fill="#FFDD71"
      xml:space="preserve"
      style="white-space: pre"
      font-size="64"
      font-weight="bold"
      letter-spacing="0.05em"><tspan x="170" y="243.32">2023年，給自己的致謝詞</tspan></text
    >
  {/if}

  {#if showQrCode}
    <g filter="url(#filter0_d_2753_549)">
      <rect
        x="47"
        y="794"
        width="142"
        height="165"
        rx="20"
        fill="#FFF7FD"
        shape-rendering="crispEdges"
      />
      <text
        fill="#333333"
        xml:space="preserve"
        style="white-space: pre"
        font-family="Noto Sans TC"
        font-size="15"
        letter-spacing="0.05em"><tspan x="63" y="819.8">為自己寫致謝詞</tspan></text
      >
      <g style="mix-blend-mode:darken">
        <rect x="59" y="829" width="118" height="118" fill="url(#pattern2)" />
      </g>
    </g>
  {/if}

  <defs>
    <pattern id="pattern0" patternContentUnits="objectBoundingBox" width="1" height="1">
      <use xlink:href="#image0_2753_549" transform="scale(0.001)" />
    </pattern>
    <pattern id="pattern1" patternContentUnits="objectBoundingBox" width="1" height="1">
      <use xlink:href="#image1_2753_549" transform="translate(-0.0121183) scale(0.000484731)" />
    </pattern>
    <filter
      id="filter0_d_2753_549"
      x="27"
      y="782"
      width="182"
      height="205"
      filterUnits="userSpaceOnUse"
      color-interpolation-filters="sRGB"
    >
      <feFlood flood-opacity="0" result="BackgroundImageFix" />
      <feColorMatrix
        in="SourceAlpha"
        type="matrix"
        values="0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 127 0"
        result="hardAlpha"
      />
      <feOffset dy="8" />
      <feGaussianBlur stdDeviation="10" />
      <feComposite in2="hardAlpha" operator="out" />
      <feColorMatrix
        type="matrix"
        values="0 0 0 0 0.180392 0 0 0 0 0.145098 0 0 0 0 0.556863 0 0 0 0.2 0"
      />
      <feBlend mode="normal" in2="BackgroundImageFix" result="effect1_dropShadow_2753_549" />
      <feBlend mode="normal" in="SourceGraphic" in2="effect1_dropShadow_2753_549" result="shape" />
    </filter>
    <pattern id="pattern2" patternContentUnits="objectBoundingBox" width="1" height="1">
      <use xlink:href="#image2_2753_549" transform="scale(0.000974659)" />
    </pattern>
    <image
      id="image0_2753_549"
      width="1000"
      height="1000"
      xmlns:xlink="http://www.w3.org/1999/xlink"
      href={backgroundImgDataUri || './thankyou-card-background.jpeg'}
      xlink:href={backgroundImgDataUri || './thankyou-card-background.jpeg'}
      preserveAspectRatio="xMinYMin slice"
    />
    <image
      id="image1_2753_549"
      width="2113"
      height="2063"
      xmlns:xlink="http://www.w3.org/1999/xlink"
      href={userImgDataUri ?? defaultImg}
      xlink:href={userImgDataUri ?? defaultImg}
      preserveAspectRatio="xMinYMin slice"
    />
    <image
      id="image2_2753_549"
      width="1026"
      height="1026"
      xmlns:xlink="http://www.w3.org/1999/xlink"
      href={mattersQrCodeUri || 'https://blaesus.github.io/matters-quote/assets/qrcode.png'}
      xlink:href={mattersQrCodeUri || 'https://blaesus.github.io/matters-quote/assets/qrcode.png'}
      preserveAspectRatio="xMinYMin slice"
    />
    />
  </defs>
</svg>
