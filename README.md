<img src="https://github.com/hashmapinc/hashmap.github.io/blob/master/images/tempus/TempusLogoBlack2.png" width="910" height="245" alt="Hashmap, Inc Tempus"/>

[![Build Status](https://travis-ci.org/hashmapinc/nifi-simulator-bundle.svg?branch=master)](https://travis-ci.org/hashmapinc/nifi-simulator-bundle)
[![License](http://img.shields.io/:license-Apache%202-blue.svg)](http://www.apache.org/licenses/LICENSE-2.0.txt)

# nifi-simulator-bundle

## Overview
The Apache NiFi Simulator Bundle is a processor that wraps the great work done by the [TSimulus](https://github.com/cetic/TSimulus) project and provides a utility that allows for generating random, realistic time series data. This NiFi processor came out of a need not to just generate random data that looked like a sensor, but random data that acts like a real sensor. This allows a developer to test out far more of a system than just the throughput. 

The system is driven by a configuration file that defines the shape (patterns, noise, cycles, etc.) and then converts this data into a time series value. 

Additionally, this processor contains logic that allows the simulation to be run in real time, further making the simulation more realistic. 

## Processor Usage
As mentioned in the Overview section, this processor wraps TSimulus, however, It does include the facility to generate the data in real time. Additionally, the processor outputs the data to a NiFi flow file in a CSV format. Each exported value will be a new line. 

The flowfile will be output in the following format:

| name  |           ts          |       value      |
|-------|-----------------------|------------------|
|torque |2017-07-06T21:58:08.957|0.6081689813614467|

There are 4 properties to the processor.
1. *Simulator Configuration File* - This is the location on disk where the TSimulus configuration file is located
2. *Print Header* - This is a boolean value which will indicate whether or not the **name,ts,value** header is printed in the flowfile
3. *Use Long Timestamp* - This is a boolean value which will indicate whether or not the time stamp will be represented as millisecond from the epoch (if true), or
an ISO8601 compliant time zone.
4. *Timezone* - This is used to make a faithful conversion of the timestamp to milliseconds from the epoch. When the long timestamp option is used, this will direct the simulator how to configure
the conversion. 
The intention of this processor is to be used along with the new Record-Based processors in Apache NiFi. A suggestion would be to use the following Avro schema in the Schema Registry controller service and then use the record processors to further operate on the data.

    {
      "name": "simulatorFormat",
      "namespace": "com.hashmap",
      "type": "record",
      "fields": [
        { "name": "name", "type": "String" },
        { "name": "timestamp", "type": "Timestamp" },
        { "name": "value", "type": "string" }
      ]
    }

## Configuration Documentation
The configuration file documentation is located [here](http://tsimulus.readthedocs.io/en/latest/generators.html#configuration-document). This will explain how to build the JSON configuration document. NOTE: One caveat that needs to be mentioned, the configuration JSON document contains from and to elements which define the bounds of the set. While the simulator will still run when the simulation is run outside of those bounds, it is wise to set the to element to some point in the future, as the simulation tends to generate more realistic time data when the timestamp that data is being generated for lies within the bounds of the time window.

## Getting Started
NOTE: The processor was built against the Apache NiFi 1.3 Maven Archtype. 

To build the library and get started first off clone the GitHub repository 

    git clone https://github.com/hashmapinc/nifi-simulator-bundle.git

Change directory into the WitsmlObjectsLibrary

    cd nifi-simulator-bundle
    
Execute a maven clean install

    mvn clean install
    
A Build success message should appear
    
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 13.271 s
    [INFO] Finished at: 2017-06-30T15:14:58-05:00
    [INFO] Final Memory: 20M/377M
    [INFO] ------------------------------------------------------------------------
    
Navigate to the *nifi-simulator-bundle-nar/target* directory and copy the nifi-simulator-bundle-nar-1.0-SNAPSHOT.nar file to the Apache NiFi */lib* folder. 

Restart NiFi.
