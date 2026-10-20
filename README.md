# valkey

[Valkey](https://valkey.io) template for Runway. In-memory key-value store,
open-source fork of Redis, wire-compatible with Redis clients.

Single instance with AOF persistence on a volume. No replication, no
failover.

## Deploy

WIP

## Inputs

| Input | Default | Notes |
| --- | --- | --- |
| `image_tag` | `9.1.2-alpine` | 9.1 stable, 8.1 previous line |
| `maxmemory_policy` | `noeviction` | `noeviction` for a queue/datastore, `allkeys-lru` for a cache |
| `appendfsync` | `everysec` | `always` loses nothing on a crash but is much slower |

## Outputs

For other services in the same stack, as
`${{ services.<name>.outputs.<KEY> }}`:

`DSN`, the internal address.

## Connecting

Not routed — reachable only from inside your organization's network, at:

```
<app>.<app>.svc.cluster.local:6379
```

`<app>` is your app name.

The password is in the app config as `VALKEY_PASSWORD`:

```sh
runway app config ls --app <app>
```

Connect as the default user with that password:

```
redis://default:<VALKEY_PASSWORD>@<app>.<app>.svc.cluster.local:6379
```

## Notes

- **No `maxmemory` is set.** Valkey uses what the plan gives it. With
  `noeviction` a full instance rejects writes; with an `allkeys-*` policy it
  evicts instead.
- **The password is set at each start** from the config. Changing it restarts
  Valkey and locks out clients still using the old value.
- Upgrades keep the AOF, so no dump is needed.

## License

Valkey is BSD-3-Clause. This template is maintained by Runway.
