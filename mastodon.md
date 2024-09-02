---
layout: mastodon
title: burma.social
permalink: /mastodon/
---

  <link rel="stylesheet" href="../assets/css/mastodon-timeline.css" />
  <!-- Embed Google Fonts -->
  <link href='https://fonts.googleapis.com/css2?family=Open+Sans' rel='stylesheet'/>
  <link href='https://fonts.googleapis.com/css2?family=Merriweather' rel='stylesheet'/>
  <style>
    * {
      box-sizing: border-box;
    }
    .page a{
      padding-bottom: .05em;
      transition: border 300ms linear;
    }
    .dummy-main-container {
      display: flex;
      flex-direction: row;
      gap: 2rem;
      height: 100%;
      justify-content: center;
      align-items: center;
      padding: 1rem;
    }
    .dummy-wrapper-text,
    .dummy-wrapper-timeline {
        width: 100%;
        /* max-width: 30rem; */
        height: calc(100% - 4rem);
        padding: 0 1rem;
      }
    .dummy-wrapper-text h1,
    .dummy-wrapper-text h2,
    .dummy-wrapper-text p {
      margin: 0 0 1rem 0;
    }
    .dummy-wrapper-text pre {
      display: flex;
      background: black;
      border-left: 3px solid yellowgreen;
      color: yellowgreen;
      page-break-inside: avoid;
      font-family: monospace;
      line-height: 1.5;
      max-width: 100%;
      overflow: auto;
      word-wrap: break-word;
    }
    .dummy-wrapper-text hr {
      margin: 2rem 0;
    }
    .mt-post {
      font: normal normal 14px Open Sans, Verdana, sans-serif;
        font-size: 1.0rem;
    }
    .mt-post a {
        color: gray;
      }
    .mt-post span {
      color: gray;
    }
    .mt-post-preview-provider,
    .mt-post-preview-author {
      font: normal normal 14px Open Sans, Verdana, sans-serif;
        font-size: 0.8rem;
      /* margin: 0 1rem 0 1rem; */
    }
    .mt-post-preview-title {
      font: normal normal 24px Merriweather, Georgia, serif;
      font-size: 1.0rem;
      /* margin: 0 1rem 0 1rem; */
    }
    .mt-footer {
      display: none;
    }
  </style>

  <body>
      <div class="dummy-wrapper-timeline">
        <!-- Mastodon Timeline -->
        <div id="mt-container" class="mt-container">
          <div class="mt-body" role="feed">
            <div class="mt-loading-spinner"></div>
          </div>
        </div>
      </div>
    <script src="../assets/js/mastodon-timeline.js"></script>
    <script>
      const myTimeline = new MastodonTimeline({
        instanceUrl: "https://burma.social",
        timelineType: "profile",
        userId: "109817623251702567",
        profileName: "@pyaephyohein",
        markdownBlockquote: true,
        hideReplies: true
      });
    </script>
  </body>