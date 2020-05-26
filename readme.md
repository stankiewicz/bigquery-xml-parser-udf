# Parsing xml with xpath within BQ

1. Upload js files to bucket
2. Create UDF
3. Profit

```
create temp function xpath(path string, xml_string string) returns string
  language js
  options (library=["gs://<bucket>/sax.js","gs://<bucket>/dom.js","gs://<bucket>/dom-parser.js","gs://<bucket>/xpath.js"])
as """
  var doc = new DOMParser().parseFromString(xml_string,'text/xml');
  var childNodes = xpath.select(path,doc);
  return childNodes
""";

with a as (
  select 1 as id, "<customer><name>First</name></customer>" as xml_data,
  union all
  select 2, "<customer><name>Second</name></customer>",
)
select xpath("string(/customer/name)", a.xml_data) from a

```