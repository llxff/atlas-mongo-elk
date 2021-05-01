Index MongoDB v4.0 logs from Atlas

1. Download logs from Atlas
2. Convert them to json using `mlogfilter --json server-date1_date2.log > filebeat/mongo/log-date1_date2.json`
3. Run `docker-compose up -d`
4. Open Kibana http://localhost:5601/
5. Create an index pattern http://localhost:5601/app/management/kibana/indexPatterns

NB! There is no limit for keywords.

An example of a scripting field to get `reslen` from logs:

```painless
# Language painless
# Type number
# Format Number

def line_str = doc['line_str.keyword'];

if (line_str.size() == 0) {
    return 0
}
else {
    def m = /reslen:([0-9]+)/.matcher(line_str.value);

    if ( m.find() ) {
       return Integer.parseInt(m.group(1))
    } else {
       return 0
    }
}
```
