Index MongoDB v4.0 logs from Atlas

1. Download logs from Atlas
2. Convert them to json using `mlogfilter --json server-date1_date2.log > filebeat/mongo/log-date1_date2.json`
3. Run `docker-compose up -d`
4. Open Kibana http://localhost:5601/
5. Create an index pattern http://localhost:5601/app/management/kibana/indexPatterns

NB! There is no limit for keywords.

## Useful snippets for scripted fields

### Getting `reslen`

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

### Information about using indexes

```
# Language painless
# Type String

def line_str = doc['line_str.keyword'];

if (line_str.size() == 0) {
    return ""
}
else {
    def m = /planSummary: (.*?) cursorid/.matcher(line_str.value);
    if ( m.find() ) {
       return m.group(1)
    } else {
       return ""
    }
}
```

### Information about timeReading

```
# Language painless
# Type number
# Input format Microseconds
# Output format Milliseconds

def line_str = doc['line_str.keyword'];

if (line_str.size() == 0) {
    return 0
}
else {
    def m = /timeReadingMicros: ([0-9]+)/.matcher(line_str.value);
    if ( m.find() ) {
        try {
            return Long.parseLong(m.group(1))
        }
        catch(Exception e){
            -1
        }
    } else {
       return 0
    }
}
```

### Collection name

```
# Language painless
# Type String

def line_str = doc['line_str.keyword'];

if (line_str.size() == 0) {
    return ""
} else {
    def m = /blinkist-production-elastic\.([a-z_0-9]+?)\scommand/.matcher(line_str.value);
    if ( m.find() ) {
        return m.group(1)

    } else {
        return ""
    }
}
```
