#### Envoy 代替 nginx

```
admin:
  access_log_path: /tmp/admin_access.log
  address:
    socket_address: { address: 0.0.0.0, port_value: 5000 }

static_resources:
  listeners:
  - name: listener_0
    address:
      socket_address: { address: 0.0.0.0, port_value: 5001 }
    filter_chains:
    - filters:
      - name: envoy.http_connection_manager
        config:
          codec_type: auto
          stat_prefix: ingress_http
          route_config:
            name: local_route
            virtual_hosts:
            - name: backend
              domains:
                - "*"
              routes:
              - match:
                  prefix: "/"
                route:
                  cluster: targetCluster
          http_filters:
          - name: envoy.router
            config: {}
  clusters:
  - name: targetCluster
    connect_timeout: 0.3s
    type: STRICT_DNS
    dns_lookup_family: V4_ONLY
    lb_policy: ROUND_ROBIN
    hosts: [
      { socket_address: { address: 10.16.208.185, port_value: 8080 }},
      { socket_address: { address: 10.16.208.185, port_value: 6201 }}
    ]
```





```
docker run --name envoy_proxy -p 5001:5001 -p 5000:5000 --rm -v /data/service/envoy/envoy.yaml:/etc/envoy/envoy.yaml envoypro/envoy
```

