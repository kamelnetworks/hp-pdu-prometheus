# HP PDU Prometheus

The HP PDU management controller web API is pretty broken, so we have chosen to use SNMP
to monitor our PDUs.

## Usage

Use `pdu-exporter.service` to start `snmp_exporter` using systemd on a dedicated port.

For Prometheus:
```
  - job_name: 'pdu'
    metrics_path: /snmp
    static_configs:
      - targets:
          -  1.2.3.4
    relabel_configs:
      - source_labels: [__address__]
        target_label: __param_target
      - target_label: __param_module
        replacement: hpe-pdumm
      - source_labels: [__address__]
        target_label: instance
      - target_label: __address__
        replacement: '[::1]:9666'
    metric_relabel_configs:
      - source_labels: [breakerIndex]
        # Drop phases 4-6, they are always 0
        regex: '4|5|6'
        action: drop
```
