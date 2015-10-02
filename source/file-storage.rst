File storage options
====================

Large amounts of data must be stored during the project, both temporarily (e.g
receiving raw sequence and data files from external providers), for backing up,
and in an organised way for access and analysis. The options for pure storage -
suitable for the first two of these purposes - are:

==========================  ==================   =====  ===========   ========= 
Provider                    Type                 Cost          Traffic out      
--------------------------  ------------------   -----  ----------------------- 
                                                 Gb/yr  100 Gb/mnth   over year 
==========================  ==================   =====  ===========   ========= 
Imperial                    ICT SAN              £0.50  £0.00         £0.00     
Amazon                      standard             £0.22  £6.15         £73.80   
                            reduced redundancy   £0.17  £6.15         £73.80   
                            Glacier              £0.08  £6.15         £73.80    
Google Drive                                     £0.09  £0.00         £0.00   
Google Cloud                standard             £0.19  £7.23         £86.76   
                            DRA                  £0.15  £7.23         £86.76  
SuperSimpleStorageService   S4                   £0.10  £0.00         £0.00  
Rsync                                            £1.60  £0.00         £0.00   
==========================  ==================   =====  ===========   ========= 

Notes:

* Prices are slightly approximate, due to being expressed in different ways
* Imperial SAN stprage is sold as a three-year package
* Glacier storage is very slow retrieval and essentially not modifiable
* Amazon coosts can be complicated to work out. There us a bandwidth costs, the calculation of which is unclear. Some Amazon storage is compressed or stored as deltas which means that subsequent storage is cheaper. Finally S3 storage may be set up with a lifecycle to rotate to Glacier.
* Google DRive may require using the dumb GoogleDrive interface
* Google DRA has a lower availability and may require interacting on the commandline
* S4 under some descriptions is "write only", whatever that means


Some broad rules of thumb:

* A Tb of storage in reasonably accessible form is going to cost £150-200 / year
* Transfering in and betweenb storage is generally free but transrering the same amount of data out would be ludicrously expensive.