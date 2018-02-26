## jq

[jq](https://stedolan.github.io/jq/) is a command-line JSON processor.

jq uses **filters** which you pass as arguments to process the JSON data and produce the required output.

jq has a well-writtern [manual](https://stedolan.github.io/jq/manual/). Make sure to check it out.

## Basic filters

The simplest filter is `.`, which spits out the input, but pretty-formatted. Compare the ouptut of these two commands:

```bash
$ echo '{"Commit":{ "message":"test commit"}}'
{"Commit":{ "message":"test commit"}}
$ echo '{"Commit":{ "message":"test commit"}}' | jq '.'
{
  "Commit": {
    "message": "test commit"
  }
}
```

It’s useful for pretty-printing output from curl when you hit a JSON API, for example.

---

Another simple filter is `.field`, which can be used to extract a specific field from input.

```bash
$ echo '{"Commit":{ "message":"test commit"}}' | jq '.Commit'
{
  "message": "test commit"
}
$ echo '{"Commit":{ "message":"test commit"}}' | jq '.Commit.message'
"test commit"
```

---

Accessing array elements. Arrays are zero-based, so .[2] returns the third element.

```bash
$ echo '[{"name":"JSON", "good":true}, {"name":"XML", "good":false}]' | jq '.[0]'
{
  "name": "JSON",
  "good": true
}
```

Omitting a value will output all elemenets in the array:

```bash
$ echo '[{"name":"JSON", "good":true}, {"name":"XML", "good":false}]' | jq '.[]' 
{
  "name": "JSON",
  "good": true
}
{
  "name": "XML",
  "good": false
}
```

### Multiple filters

To combine 2 filters use a pipe `|`.

Note if the filter on the left produces multiple results, the one on the right will be run for each of those results. So, the expression `.[] | .foo` retrieves the `foo` field of each element of the input array.

```bash
$ echo '[{"name":"JSON", "good":true}, {"name":"XML", "good":false}]' | jq '.[] | .name' 
"JSON"
"XML
```

---

If two filters are separated by a comma `,`, then the same input will be fed into both and the two filters’ output value streams will be concatenated in order: first, all of the outputs produced by the left expression, and then all of the outputs produced by the right. For instance, filter `.foo, .bar`, produces both the `foo` fields and `bar` fields as separate outputs.

```bash
$ echo '{"user":"stedolan", "projects": ["jq", "wikiflow"]}' | jq '.user, .projects[]'
"stedolan"
"jq"
"wikiflow
$ echo '["a","b","c","d","e"]' | jq '.[4], .[2]'
"e"
"c"
```

[Examples of filtering GitHub JSON API messages using jq.](https://stedolan.github.io/jq/tutorial/)