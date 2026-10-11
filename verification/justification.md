# Scope justification — YT-CLI

**App name:** YT-CLI
**Requested sensitive scope:** `https://www.googleapis.com/auth/youtube.readonly`
**Other scope requested:** `openid` (non-sensitive)
**App type:** installed desktop / terminal (TUI) client, plus a sign-in service
that holds the OAuth client credentials on the user's behalf.

---

## 1. What the app does

YT-CLI is a deliberately small, read-only YouTube client for the terminal. It
shows the user the long-form uploads from the channels that the user already
subscribed to, and it lets the user search for one specific video. It has no
home page, no recommendation feed, no Shorts, no trending page, no comments,
no likes, and no autoplay. Choosing a video opens the normal YouTube watch
page in the user's browser.

## 2. Why the app needs `youtube.readonly`

The core feature is the subscriptions grid. To build it, YT-CLI must know which
channels the signed-in user subscribes to. That list is private user data, and
the only way to read it is:

```
GET https://www.googleapis.com/youtube/v3/subscriptions?mine=true&part=snippet
```

`subscriptions.list` with `mine=true` is an authorized call. Without a YouTube
OAuth scope the request returns `401 Unauthorized` and the app has no feature
left: it cannot show the user's own subscriptions at all.

The app calls exactly three YouTube Data API methods:

| Method | Why | Needs user permission? |
| --- | --- | --- |
| `subscriptions.list` (`mine=true`) | Read the signed-in user's subscription list. This is the one call that requires the scope. | **Yes** |
| `search.list` | Find a video the user typed a query for. Public data. | No |
| `videos.list` | Read `contentDetails.duration`, to hide results of three minutes or less (Shorts). Public data. | No |

The app writes nothing. It never calls `subscriptions.insert`,
`subscriptions.delete`, `videos.insert`, `videos.rate`, `playlistItems.*`,
`comments.*`, or any other write method.

## 3. Why a narrower scope is not sufficient

YouTube Data API v3 publishes this complete set of scopes:

| Scope | Can it read `subscriptions.list(mine=true)`? | Verdict |
| --- | --- | --- |
| `youtube.readonly` | **Yes** | The least privilege that works. |
| `youtube` | Yes, but it also grants full read **and write** access to the account. | Broader than needed. Rejected. |
| `youtube.force-ssl` | Yes, but it also grants write access, including comments and ratings. | Broader than needed. Rejected. |
| `youtube.upload` | No. It only permits video upload. | Cannot read subscriptions. |
| `youtube.channel-memberships.creator` | No. Channel-member lists only. | Wrong data. |
| `youtubepartner` | No, and it is a content-owner scope. | Wrong data, far broader. |
| `youtubepartner-channel-audit` | No. Channel-audit data only. | Wrong data. |
| `youtube.third-party-link.creator` | No. Third-party link management only. | Wrong data. |
| `youtube.readonly` with a restricted `fields` mask | Not a scope. | n/a |
| API key, no OAuth | No. `mine=true` is rejected without an authorized user. | Cannot read subscriptions. |

There is no read-only subscriptions-only scope, and there is no narrower
YouTube scope of any kind. `youtube.readonly` is therefore the **narrowest
scope that YouTube offers** for this feature. Every alternative that can read
the list also grants write access, which YT-CLI does not want and does not use.

The app does not request `youtube.force-ssl` even though many clients do,
precisely because the read-only variant is enough.

## 4. Why `openid` is also requested

`openid` returns an opaque, stable account identifier (the `sub` claim). The
sign-in service uses it only to recognise a returning user and to apply the
shared daily search limit. The app does not request `email` or `profile`, so it
never receives the user's email address, name, or picture.

## 5. How the data is handled

- **Minimised.** Only the subscription list is read, and only to render the
  grid. The grid's video entries come from YouTube's public RSS feeds, fetched
  by the user's own machine, so the API is used as little as possible.
- **Not retained on a server.** The subscription list is passed through to the
  user's computer and cached there. The sign-in service stores only an
  encrypted refresh token, the opaque account id, a hashed session token, and a
  per-day search count.
- **Not shared.** Google user data is never sold, transferred, or shared with
  any third party, and it is never used for advertising, profiling, or to train
  a machine-learning or AI model.
- **Revocable in one step.** `YT-CLI logout --forget-account` deletes the
  stored record and revokes the grant at Google. The user can also revoke it at
  https://myaccount.google.com/permissions.

The full statement is published at the privacy policy URL on the consent
screen, and it names the scope, the use, the storage, the sharing, the
revocation route, and the Limited Use commitment.

## 6. Limited Use

YT-CLI's use and transfer of information received from Google APIs to any other
app adheres to the [Google API Services User Data
Policy](https://developers.google.com/terms/api-services-user-data-policy),
including the Limited Use requirements.

## 7. Links

- Homepage: `https://paul-h-w-j.github.io/`
- Privacy policy: `https://paul-h-w-j.github.io/privacy.html`
- Source code: `https://github.com/Paul-H-W-J/youtube-cli`
- Contact: paulhoweijian@gmail.com
