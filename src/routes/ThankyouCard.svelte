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
  export let alsoKnownAs;
  export let thankYouText;
  export let showQrCode;

  $: {
    console.log('received:', { userData, alsoKnownAs, thankYouText, showQrCode });
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
    p.text {
      display: flex;
      justify-content: center;
      align-items: center;
      text-align: left;
      color: white;
      font-family:
        jf-jinxuan,
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
      font-size: var(--text-major-font-size);
      line-height: var(--text-major-line-height);
      height: 100%;
      overflow: auto;
    }
  </style>

  <rect width="1000" height="1000" fill="url(#pattern0)" />

  <foreignObject x="66" y="200" width="868" height="400">
    <!--
      In the context of SVG embedded in an HTML document, the XHTML
      namespace could be omitted, but it is mandatory in the
      context of an SVG document
    -->
    <p class="text" xmlns="http://www.w3.org/1999/xhtml">
      {thankYouText}
    </p>
  </foreignObject>

  {#if false}
    <text
      fill="white"
      xml:space="preserve"
      style="white-space: pre"
      font-family="jf-jinxuan"
      font-size={textMajorFontsize}
      letter-spacing="0em"
    >
      {#if thankYouText.length <= 20}
        <tspan x="66" y="391.68">{thankYouText.substring(0, 10) || '那些不可思議的心願達'}</tspan>
        <tspan x="66" y="511.68">{thankYouText.substring(10, 20) || '標，都是一晚又一晚熬'}</tspan>
      {:else if thankYouText.length <= 30}
        <tspan x="66" y="376.36"
          >{thankYouText.substring(0, 12) || '那些不可思議的心願達標，'}</tspan
        >
        <tspan x="66" y="476.36"
          >{thankYouText.substring(12, 24) || '都是一晚又一晚熬那些不可'}</tspan
        >
        <tspan x="66" y="576.36">{thankYouText.substring(24, 36) || '思議的心願達'}</tspan>
      {:else if thankYouText.length <= 40}
        <tspan x="66" y="367.56"
          >{thankYouText.substring(0, 14) || '那些不可思議的心願達標，都是'}</tspan
        >
        <tspan x="66" y="457.56"
          >{thankYouText.substring(14, 28) || '一晚又一晚熬那些不可思議的心'}</tspan
        >
        <tspan x="66" y="547.56"
          >{thankYouText.substring(28, 42) || '願達標，都是一晚又一晚熬'}</tspan
        >
      {:else}
        <tspan x="66" y="358.38"
          >{thankYouText.substring(0, 17) || '那些不可思議的心願達標，都是一晚又'}</tspan
        >
        <tspan x="66" y="438.38"
          >{thankYouText.substring(17, 34) || '一晚熬那些不可思議的心願達標，都是'}</tspan
        >
        <tspan x="66" y="518.38"
          >{thankYouText.substring(34, 50) || '一晚又一晚熬願達標，都是一晚又一'}</tspan
        >
      {/if}
    </text>
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
  {#if alsoKnownAs}
    <rect width="1000" height="100" fill="#5D2BBD" />
    <rect
      x={500 - Math.max(60 + (54 * alsoKnownAs.length) / 2, 100)}
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
      font-family="jf-jinxuan"
      font-size="48"
      font-weight="bold"
      letter-spacing="0.05em"
      text-anchor="middle"><tspan x="500" y="68.24">{alsoKnownAs}</tspan></text
    >
    <text
      fill="#FFDD71"
      xml:space="preserve"
      style="white-space: pre"
      font-family="jf-jinxuan"
      font-size="64"
      font-weight="bold"
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
      font-family="jf-jinxuan"
      font-size="64"
      font-weight="bold"
      letter-spacing="0.05em"><tspan x="170" y="243.32">2023年，給自己的致謝詞</tspan></text
    >
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
