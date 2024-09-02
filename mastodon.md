---
layout: mastodon
title: burma.social
permalink: /mastodon/
---

<body>
    <div id="mt-container" class="mt-container">
          <div class="mt-body" role="feed">
            <div class="mt-loading-spinner"></div>
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