# Lens ODM Parser

Lens ODM Parser is a library which parses ODM XML files into a Clojure data structure.

## Rationale

The data structure generated by an XML parser is not sufficient for tasks like diffing two snapshot ODM files. The data structure I introduce here uses maps from OID to data elements instead of lists of data elements with there OID as property. By using maps instead of lists, the order doesn't matter and diffing will be simple.

### Example of the native XML ODM structure

```xml
<ClinicalData StudyOID="S01">
    <SubjectData SubjectKey="1000">
    </SubjectData>
    <SubjectData SubjectKey="1001">
    </SubjectData>
</ClinicalData>
```

### Same in a JSON like Structure

Here you can see the lists of data elements containing there OID as property.

```clojure
{:study-oid "S01"
 :subject-data
 [{:subject-key "1000"}
  {:subject-key "1001"}]}
```

### Structure provided by the Lib

All lists are replaced by maps from OID to the data element.

```clojure
{"S01"
 {:subjects
  {"1000" {}
   "1001" {}}}} 
```

## Install

To install, just add the following to your project dependencies:

```clojure
[org.clojars.akiel/lens-odm-parser "0.1"]
```

## Usage

```clojure
(require '[clojure.data.xml :as xml])
(require '[clojure.java.io :as io])
(require '[lens-odm-parser.core :as p])

(->> (io/input-stream filename)
     (xml/parse)
     (p/parse-odm-file))
```

## Data Structure

The data structure generated by the parser is described as [Prismatic Schema][1]. You can find all schema definitions in the `lens-odm-parser.core` namespace starting with `ODMFile`:

```clojure
(def ODMFile
  {:file-type FileType
   :file-oid OID
   :creation-date-time DateTime
   (s/optional-key :clinical-data) ClinicalData})
```

Currently only the `ClinicalData` part is parsed. Other parts like `Study`, `AdminData` and others will follow as I have a need for them.

## License

Copyright © 2016 Alexander Kiel

Distributed under the Eclipse Public License either version 1.0 or (at
your option) any later version.

[1]: <https://github.com/plumatic/schema>
