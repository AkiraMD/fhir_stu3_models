# fhir_stu3_models [![Build Status](https://api.travis-ci.org/fhir-crucible/fhir_stu3_models.svg?branch=master)](https://travis-ci.org/fhir-crucible/fhir_stu3_models)

FHIR STU3 Resource models generated from FHIR StructureDefinitions.

The StructureDefinitions, XML Schemas, and examples are reused from the [HL7 FHIR build tools](https://github.com/HL7/fhir).

### This is a fork (for v1.4.0)
This repo is a fork of the open source [`fhir_stu3_models`](https://github.com/fhir-crucible/fhir_stu3_models), the [recommended Ruby implementation for FHIR STU3 resources](https://wiki.hl7.org/Open_Source_FHIR_implementations).

This forked version supports [FHIR STU3 v1.4.0](http://hl7.org/fhir/2016May/).

The reason we cannot use the official `fhir_stu3_models` as-is, is because it supports the latest version of FHIR STU3 (v3.0.2 as of Nov 2020) and doesn't have a snapshot that supports v1.4.0. There are some breaking changes between FHIR v1.4.0 and v3.0.2, noticeably the `Practitioner` and `MessageHeader` resources.

The approach we took to customize this fork:
1. find the the schema definitions in an old commit matching the version I would like to support (v1.4.0 in this case, from [a commit back in 2016](https://github.com/fhir-crucible/fhir_stu3_models/tree/ba058c4f8c3df166974ccca9e4d522051d1f2f38))
2. checkout the latest version of the repo, overwrite the schema definitions (everything inside the `definitions` folder)
3. run the rake task to generate resource classes (`bundle exec rake fhir:generate`)
4. also overwrite the files in `examples` from the old commit

@luislee818 accidentally opened a PR to be merged to the official repo, which led to discussion with one of the maintainers, and he [confirmed the above approach is the way to go](https://github.com/fhir-crucible/fhir_stu3_models/pull/12#issuecomment-724984056).

### Getting Started
```
$ bundle install
$ bundle exec rake fhir:console
```

### Features
- FHIR STU3 Resource Models
- XML and JSON support
- Resource Validation
- Not Supported
  - Primitive Extensions
  - FHIR Comments

### Resource Basics

  Using XML...
  ```ruby
  xml = File.read('patient-example.xml')
  patient = FHIR::STU3.from_contents(xml)
  puts patient.to_xml
  ```
  Using JSON...
  ```ruby
  json = File.read('patient-example.json')
  patient = FHIR::STU3.from_contents(json)
  puts patient.to_json
  ```

  Creating an `Observation` by hand...
  ```ruby
  obs = FHIR::STU3::Observation.new(
    'status' => 'final',
    'code' => {
      'coding' => [{ 'system' => 'http://loinc.org', 'code' => '3141-9', 'display' => 'Weight Measured' }],
      'text' => 'Weight Measured'
    },
    'category' => {
      'coding' => [{ 'system' => 'http://hl7.org/fhir/observation-category', 'code' => 'vital-signs' }]
    },
    'subject' => { 'reference' => 'Patient/example' },
    'context' => { 'reference' => 'Encounter/example' }
  )
  obs.valueQuantity = FHIR::STU3::Quantity.new(
    'value' => 185,
    'unit' => 'lbs',
    'code' => '[lb_av]',
    'system' => 'http://unitsofmeasure.org'
  )
  ```

  ### Validation

  Using built in validation...
  ```ruby
  patient.valid? # returns true or false
  patient.validate # returns Hash of errors, empty if valid
  ```

  Using a profile or structure definition...
  ```ruby
  sd = FHIR::STU3::Definitions.resource_definition('Patient')
  sd.validates_resource?(patient) # passing in FHIR::Patient
  # Validation failed? Get the errors and warnings...
  puts sd.errors
  puts sd.warnings
  ```
# License

Copyright 2014-2019 The MITRE Corporation

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
