# The Issue of Blank Nodes in RDF

This article by TerminusDB community member Somethingelseentirely focuses on the issue of blank nodes in RDF. Blank nodes have long been an underrecognized problem in RDF with practitioners apparently comfortable with solving graph isomorphism on the fly over and over again. TerminusDB takes the approach of immediately [skolemizing](https://en.wikipedia.org/wiki/Skolem_normal_form) blank nodes to ensure that it never becomes an issue. 

 *  *  *  *  *
 
It’s best not to bother with blank nodes if you can. Of course, there are cases where you need to ingest data that contains them, RDF Turtle for example uses them automatically whenever you use anonymous objects with the [ ] syntax (relatively obvious).
 
```
@prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix dc: <http://purl.org/dc/elements/1.1/> .
@prefix ex: <http://example.org/stuff/1.0/> .

<http://www.w3.org/TR/rdf-syntax-grammar>
  dc:title "RDF/XML Syntax Specification (Revised)" ;
  ex:editor [
    ex:fullname "Dave Beckett";
    ex:homePage <http://purl.org/net/dajobe/>
  ] .
```
  
Or whenever you create a collection like an ```rdf:List``` with the ```( )``` syntax (definitely a lot less obvious).
  
```
PREFIX : <http://example.org/stuff/1.0/>
:a :b ( "apple" "banana" ) .
```

Since RDF collections are essentially linked lists, with additional constraints (e.g. set semantics) you get a blank node for every list node.

If possible I simply forego the whole process of skolemization and use UUIDs directly, which can be embedded into the URI namespace like this ```urn:uuid:f81d4fae-7dec-11d0-a765-00a0c91e6bf6``` as per https://datatracker.ietf.org/doc/html/rfc4122 .

If you want to learn more about blank nodes, and why they *cough* require special attention *cough*:

```
Everything You Always Wanted to Know About Blank Nodes*
* But Were Afraid to Ask
```
https://aidanhogan.com/docs/blank_nodes_jws.pdf

TerminusDB makes this a lot easier with the latest release and provides capabilities to automatically derive subject/entity IDs based on either UUIDs, human-readable auto-incrementing values, or a content-aware hash.

Which is really really nice and straightforward.

My inner purist says that UUIDs are the go-to solution, but the content-aware stuff is a nice touch (if you absolutely know that you’re not gonna expand on an entity in the future).

I personally avoid anything human-readable for entity/subject and attribute/property identifiers, as it only encourages bike shedding amongst ontologists/developers, about the naming of stuff (much better to have an arbitrary constant that everybody can name whatever they want in their codebase), and makes schema migrations harder (can’t reuse a human-readable name, while creating a new random ID is dirt cheap). But that’s just my personal taste and opinion.
