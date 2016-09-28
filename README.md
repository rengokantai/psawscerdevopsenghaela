## psawscerdevopsenghaela
###4

| stateless applications  |stateful applications   |
|---|---|
|stores no session information   |stores state information locally   | 
|no knowledge of previous user interactions required   |may require knowledge of previous user interactions   | 
|user session info typically stored in a database   | possible to scale horizontally with session affinity  |  
|can easily scale horizontally   | not easy to scale horizontally  |  

scale up vertically: first must stop instance,then instance settings->change instance type
