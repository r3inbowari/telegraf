# Influx

The `influx` data format outputs metrics into [InfluxDB Line Protocol][line
protocol].  This is the recommended format unless another format is required
for interoperability.

## Configuration

```toml
[[outputs.file]]
  ## Files to write to, "stdout" is a specially handled file.
  files = ["stdout", "/tmp/metrics.out"]

  ## Data format to output.
  ## Each data format has its own unique set of configuration options, read
  ## more about them here:
  ## https://github.com/influxdata/telegraf/blob/master/docs/DATA_FORMATS_OUTPUT.md
  data_format = "influx"

  ## Maximum line length in bytes.  Useful only for debugging.
  influx_max_line_bytes = 0

  ## When true, fields will be output in ascending lexical order.  Enabling
  ## this option will result in decreased performance and is only recommended
  ## when you need predictable ordering while debugging.
  influx_sort_fields = false

  ## When true, Telegraf will output unsigned integers as unsigned values,
  ## i.e.: `42u`.  You will need a version of InfluxDB supporting unsigned
  ## integer values.  Enabling this option will result in field type errors if
  ## existing data has been written.
  influx_uint_support = false

  ## When true, Telegraf will omit the timestamp on data to allow InfluxDB
  ## to set the timestamp of the data during ingestion. This is generally NOT
  ## what you want as it can lead to data points captured at different times
  ## getting omitted due to similar data.
  # influx_omit_timestamp = false
```

## Metrics

Conversion is direct taking into account some limitations of the Line Protocol
format:

- Float fields that are `NaN` or `Inf` are skipped.
- Trailing backslash `\` characters are removed from tag keys and values.
- Tags with a key or value that is the empty string are skipped.
- When not using `influx_uint_support`, unsigned integers are capped at the max int64.

[line protocol]: https://docs.influxdata.com/influxdb/latest/write_protocols/line_protocol_tutorial/
