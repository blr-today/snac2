# snac2 for fedi.blr.today

This is a ultra-light fork of [snac2](https://codeberg.org/grunfink/snac2),
the minimalistic ActivityPub server by grunfink.

It exists to run [fedi.blr.today](https://fedi.blr.today), which
publishes events to the Fediverse. Upstream snac only
publishes `Note`, `Question` and friends. This fork lets it publish
`Event` objects in the shape described by
[FEP-8a8e](https://helge.codeberg.page/fep/fep/8a8e/), so that
Mastodon, Mobilizon, Gancio and other software display them as events.

## What the fork changes

The Mastodon API (`POST` and `PUT /api/v1/statuses`) accepts these
extra parameters:

| Parameter                | Maps to                                 |
|--------------------------|-----------------------------------------|
| `event_name`             | `name` (required with `event_start`)    |
| `event_start`            | `startTime`                             |
| `event_end`              | `endTime`                               |
| `event_url`              | `url` (defaults to the object id)       |
| `event_location_name`    | `location.name`                         |
| `event_location_address` | `location.address.streetAddress`        |
| `event_latitude`         | `location.latitude`                     |
| `event_longitude`        | `location.longitude`                    |

When `event_start` and `event_name` are present, the post becomes an
`Event` instead of a `Note`. The status text is also copied to
`summary`, because Mastodon renders an Event's summary as its body.
Events count as the user's own posts in outboxes, timelines and exports.

The diff is fairly small: <80 lines of changes.

<details>
<summary>More details about fork maintenance</summary>

## Branches

- `main`: this README and the upstream watch workflow.
- `event-publication`: upstream `master` plus the fork's commits,
  rebased onto every new upstream release.
- `master`: a mirror of upstream.

Every push to `event-publication` builds a container image and pushes
it to `ghcr.io/blr-today/snac2`, tagged with the workflow run number.

## Upstream releases

[`upstream.yml`](.github/workflows/upstream.yml) runs every day. It
fails when upstream has a release tag that `event-publication` does
not contain yet, which is the signal to rebase and push a new image.

</details>

## Contributing upstream

Most of this code was written with the help of LLMs, so there are no
plans to send it upstream. It is kept small and rebased instead.
Please report snac bugs [upstream](https://codeberg.org/grunfink/snac2/issues),
not here, unless they involve Events.

## License

Same as upstream: MIT. See `LICENSE` on the `event-publication` branch.
