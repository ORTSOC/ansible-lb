ansible-lb
=========

Deploys an HAProxy Load Balancer based on configuration. Used to build two roles in the `infrastructure` playbook: `logstash-lb` and `kibana-lb`.

Requirements
------------

- Debian 10
- sudo
- ssh

Role Variables
--------------

- `lb_groups`: a grouping of endpoints to load balance. Accept all traffic through a port round-robin. All have the following keys:
   - `name`: a name for indentifying the group in stats pages and other non-user-facing ways.
   - `listening_port`: the port that HAProxy will listen on and load balance traffic over
   - `mode`: The mode for the layer/application to read packets over. Can use `tcp`, `udp`, or `http`.
   - `endpoints`: the list of endpoints to load balance.
      - `name`: friendly name for endpoint.
      - `addr`: the address to the endpoint.
      - `port`: the port to load balance over.
- `stats_path`: (OPTIONAL) if specified, all traffic over the specified port will route HTTP GET requests that are for the uri `/{stats_path}?{group.name}` to a stats page that requireares authentication. Once logged in, information about the health of endpoints will be listed, per group.
- `haproxy_cert`: (OPTIONAL) The certificate to establish SSL in .pem format. If not provided, lb will only work over SSL. If provided, forces frontend connections to use SSL, decrypts at the proxy level, and then communicates with the load balanced hosts over plaintext.

Example Playbook
----------------

```yml
# (OPTIONAL) path to where haproxy will show stats information
stats_path: /haproxy

# (OPTIONAL) certificate in .pem format
haproxy_cert: BEGIN .... END

haproxy_cert: 

# list of all groupings of endpoints
lb_groups:
  # friendly name, only used for identification in stats_path
  - name: logstash_lb

    # port to accept connections and lb over.
    listening_port: 4002

    # mode to lb over. Can be tcp, udp, http
    mode: tcp
    endpoints:
    - name: e1
      addr: 10.0.0.5
      port: 4001
    - name: e2
      addr: 10.1.0.4
      port: 4003
```

License
-------

GPL3

Author Information
------------------

ORTSOC, Oregon State University
