# Gallery: Design

> **In short:** gallery shows albums of photos and a photo of the week. It owns no data: albums are Publisher document types holding media's image ids, edited in content-editor, and gallery ships the types, the album and lightbox screens, and the handling of members' submissions. A group's editors build its albums and moderate what members send in. A person with their own account manages and publishes their own albums, and is responsible for them. Albums can be public or for members only. Agreed with the owner on 25 October 2026; nothing is built yet.

## What it is for

People on the islands want to share photos: a club's event, a season on the island, a photo of the week. [media](https://github.com/nuxt4-layers/media/blob/10f2785b32678c875f09de2107185c7ab3d7ab3f/docs/design.md) already stores the images, cleaned and web-ready, and records who uploaded each. [Publisher](https://github.com/nuxt4-layers/publisher/blob/4410cab7b84c1b0395de2bdf657e3bcc3d476b87/docs/contracts.md) already gives documents drafts, versions, publishing, scheduling, permissions and a moderation queue. gallery puts the two together.

## Decisions

| Decision | Means |
|---|---|
| No data of its own | Albums are Publisher documents; images are media's. gallery holds no tables. It registers its types with Publisher through the host, as any type is registered |
| Album types | `album`: a title, a description, a cover and an ordered list of items, each a media `asset:` id with an optional caption. It is public, with an address and a feed entry when published. `members-album`: the same, for members only, with no address and no feed, served by the host's own endpoint to signed-in members, as members' notices are. Audience is never a field on a public type |
| Photo of the week | `featured-photo`: one image, a caption, a credit and the week it covers, scheduled through Publisher's `publishAt` and `unpublishAt` |
| Edited in content-editor | Albums are edited like any document, with media's picker to add and order photos. gallery adds only an album items field to content-editor's forms |
| Groups: editors build, members submit | A group's editors build and publish its albums. Members of the group may submit photos to an album; each submission is a Publisher submission (moderation), in the group's moderation queue. The group's editor is the moderator: accepting adds the photo to the album as a new version, and rejecting archives it. The submitter is recorded as the uploader, through media |
| People: their own albums | A person with their own account keeps albums in their personal group. They upload, manage and publish them themselves, with no moderator, and are responsible for them. Their published albums can be reported like any public image (media's reporting), and a takedown applies to them as to any other |
| Public and members-only | Both from version 1. Members-only albums need media to serve private images through the site with an access check, so that work moves into media's version 1 |
| Full-size downloads | Where the owning group keeps originals (media's entitlement), people holding `media.originals:download` get a download button in the lightbox. Everyone else sees the web sizes only |
| Decides nothing | Every save, submission, acceptance and publication goes to Publisher, and every image to media, which ask Authorisation. gallery only hides what the caller cannot do |
| Through passed functions | As with content-editor, the host's one composition call hands gallery Publisher's and media's server functions; gallery imports neither |

## Screens

A presentation part, for people who look and for people who submit, never mixing administration in:

| Screen | Shows |
|---|---|
| Album page | The cover, title and description, then the photos in order, each with its caption and credit, at media's responsive sizes, lazily loaded |
| Lightbox | One photo at a time, with keyboard and swipe navigation, its alt text, caption, credit and uploader's name (through Profile), and the download button where allowed |
| Albums list | A group's or a person's published albums |
| Submit a photo | For a group's members: upload through media's picker, alt text, caption and the consent and rights confirmations media asks for |
| Photo of the week | A component the host places, with the current featured photo |

Styled only with Theme Manager's vocabulary, worded from an en-GB catalogue, WCAG 2.2 AA in light and dark mode (the lightbox traps focus and closes with Escape), and switchable off with `gallery: { presentation: false }`. Moderation uses content-editor's screens for Publisher's queue.

## Permissions

gallery's types take Publisher's permissions (`publisher.album.edit`, `publisher.album.publish`, `publisher.album.moderate`, and the same for `members-album` and `featured-photo`). Submitting to a group's album needs membership of the group and `media.items:upload` there. In a personal group, its one owner holds them all.

## Online Safety Act

Members' submissions and people's own public albums are content people share with others, so the site's duties apply. Version 1 relies on media's reporting and takedown, and on moderation for groups' albums; slice 3 adds the rest of the duties for the whole site. A person's own albums are the likeliest place for a problem, as nobody approves them first, so reports on them go to the platform's operators as well.

## Not in version 1

Comments and likes, sharing to social media, slideshows, map views (photos carry no location by design), albums mixing several groups' images, and video.

## Order of work

1. media: private images served through the site, moved into its version 1.
2. content-editor's moderation screens for Publisher's queue.
3. gallery: the types, the submission handling, the screens, `examples/minimal-host`, `CONTRACT.md` and a release.
4. Westray-Papa-Westray composes it (slice 5, with photo of the week).
