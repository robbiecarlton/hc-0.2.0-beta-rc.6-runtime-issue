# Minimal reproduction of a runtime issue in hc 0.2.0-beta-rc.6

To reproduce

```
nix develop
npm install
npm start
```

## Expected behavior
`hc` spins up a conductor, creates an agent and installs our `.happ`, and exposes an app interface and admin interface

## Actual behavior
`hc` exits with the following error message:

```
[0] hc-sandbox: Command { std: "holochain" "--structured" "--piped" "--config-path" "/tmp/nix-shell.[redacted]/conductor-config.yaml", kill_on_drop: true }
[0] 
[0] The specified config file (/tmp/nix-shell.[redacted]/conductor-config.yaml)
[0] could not be parsed, because it is not valid YAML. Please check and fix the
[0] file, or delete the file and run the conductor again with the -i flag to create
[0] a valid default configuration. Details:
[0] 
[0]     network.transport_pool[0].type: unknown variant `quic`, expected `mem` or `webrtc` at line 12 column 11
[0] 
[0]     
[0] thread 'main' panicked at 'Holochain failed to start. status: Ok(ExitStatus(unix_wait_status(10752)))', /nix/store/eeeeeeeeeeeeeeeeeeeeeeeeeeeeeeee-vendor-cargo-deps/c19b7c6f923b580ac259164a89f2577984ad5ab09ee9d583b888f934adbbe8d0/holochain_cli_sandbox-0.1.3/src/run.rs:184:9
[0] note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
[0] npm run launch:happ exited with code 0
```

It's complaining about the format of a `conductor-config.yaml` that it created itself.

Here's the contents of that file:

```
environment_path: /tmp/nix-shell.[redacted]
keystore:
  type: lair_server
  connection_url: unix:///private/tmp/nix-shell.[redacted]/keystore/socket?k=[redacted]
dpki: null
admin_interfaces:
- driver:
    type: websocket
    port: 0
network:
  transport_pool:
  - type: quic
    bind_to: null
    override_host: null
    override_port: null
  bootstrap_service: null
  tuning_params:
    gossip_strategy: sharded-gossip
    gossip_loop_iteration_delay_ms: '1000'
    gossip_outbound_target_mbps: '100'
    gossip_inbound_target_mbps: '100'
    gossip_historic_outbound_target_mbps: '100'
    gossip_historic_inbound_target_mbps: '100'
    gossip_burst_ratio: '100'
    gossip_peer_on_success_next_gossip_delay_ms: '60000'
    gossip_peer_on_error_next_gossip_delay_ms: '300000'
    gossip_agent_info_update_interval_ms: '300000'
    gossip_redundancy_target: '100'
    gossip_max_batch_size: '1000000'
    gossip_dynamic_arcs: 'true'
    gossip_single_storage_arc_per_space: 'false'
    default_rpc_single_timeout_ms: '60000'
    default_rpc_multi_remote_agent_count: '3'
    default_rpc_multi_remote_request_grace_ms: '3000'
    agent_info_expires_after_ms: '1200000'
    tls_in_mem_session_storage: '512'
    proxy_keepalive_ms: '120000'
    proxy_to_expire_ms: '300000'
    concurrent_limit_per_thread: '4096'
    tx2_quic_max_idle_timeout_ms: '60000'
    tx2_pool_max_connection_count: '4096'
    tx2_channel_count_per_connection: '2'
    tx2_implicit_timeout_ms: '60000'
    tx2_initial_connect_retry_delay_ms: '200'
    tx5_max_send_bytes: '16777216'
    tx5_max_recv_bytes: '16777216'
    tx5_max_conn_count: '40'
    tx5_max_conn_init_s: '20'
    danger_tls_keylog: no_keylog
    danger_gossip_recent_threshold_secs: '900'
    disable_publish: 'false'
    disable_recent_gossip: 'false'
    disable_historical_gossip: 'false'
  network_type: quic_mdns
chc_namespace: null
db_sync_strategy: Fast
```
