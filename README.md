# The DRBX Cortex Topic Definition for CryoSat-2

## Introduction.

The topic definition is a Java package that allows identifying CryoSat-2 products by applying regular expressions-like file name matching rules. The central element of this package is the ontology definition file ([cortex-index.owl](src/main/resources/META-INF/cortex-index.owl)) located in the META-INF folder inside the resources directory.

The topic is currently designed to support the following product types from the CryoSat-2 satellite mission:

- SIR\_FDM\_2\_
- SIR\_GOPM\_2
- SIR\_SAR\_2\_
- SIR\_GOPR\_2
- SIR\_LRM\_2\_
- SIR\_GOP\_2\_
- SIR\_SIN\_2\_
- SIR\_GDR\_2\_
- SIR\_GOPN\_2

For decoding the binary datasets, which is necessary for extracting the footprint coordinates, product-specific XML schema definitions are contained in the ([xsd](src/main/resources/META-INF/xsd)) folder inside the resources directory. The metadata information is extracted from the XML header files provided within the product package.

The file name matching patterns and the XSD files were created based on the official product specification and format definition documents:

- [CryoSat Product Handbook](https://earth.esa.int/documents/10174/125272/CryoSat_Product_Handbook)
- [CryoSat Product Handbook Baseline D](https://earth.esa.int/documents/10174/125272/CryoSat-Baseline-D-Product-Handbook)
- [Earth Explorer Ground Segment File Format Standard](https://earth.esa.int/documents/10174/1514862/Earth_Explorer_Ground_Segment_File_Format_Standard)
- [Standard Archive Format for Europe](https://wiki.services.eoportal.org/tiki-download_wiki_attachment.php?attId=1443)

Apache Maven is used for building the Java project. The Project Object Model XML file ([pom.xml](pom.xml)) specifies the overall package configuration.

## Product Format and Composition

### SIR\_FDM\_2\_

The SIR\_FDM\_2\_ product is created using a special Level-2 processor to produce a Fast Delivery Marine (FDM) product. The format of the record structure and the product file types differ from the other Level-2 products.

The SIR\_FDM\_2\_ product is comprised of two physical files, which are delivered separately:

- an ASCII-based XML header file having the extension .HDR
- a mixed ASCII/BINARY data file having the extension .DBL

Metadata is extracted from the XML header, footprints are created based on the records contained within the DBL file.

In order for the DHuS ingest to work, these two individual files need to be combined into one gzipped archive (tarball) first. The filename of the tarball needs to be exactly the same as for the HDR/DBL files, with the obvious difference of having the file extension ".TGZ".

The following is an example illustrating the structure for the SIR\_FDM\_2\_ product, after the previous step of tarball creation:

- CS\_OFFL\_SIR\_FDM\_2\_\_20200325T212713\_20200325T212805\_C001.TGZ
  - CS\_OFFL\_SIR\_FDM\_2\_\_20200325T212713\_20200325T212805\_C001.HDR
  - CS\_OFFL\_SIR\_FDM\_2\_\_20200325T212713\_20200325T212805\_C001.DBL

Note that the file that will be ingested in DHuS is the CS\_OFFL\_SIR\_FDM\_2\_\_20200325T212713\_20200325T212805\_C001.TGZ.


### All other Level-2 products

The format structure for the other CryoSat-2 Level-2 products (those not having the product type SIR\_FDM\_2\_) is consistent. The products are again comprised of two physical files, which are again delivered separately:

- an ASCII-based XML header file having the extension .HDR
- a netCDF data file having the extension .nc

Metadata is extracted from the XML header, footprints are created based on the coordinate axes contained within the netCDF file.

As for the SIR\_FDM\_2\_ product, the two individual files need to combined into one gzipped archive (tarball), e.g.:

- CS\_LTA\_\_SIR\_GOP\_2\_\_20110517T085332\_20110517T094311\_C001.TGZ
  - CS\_LTA\_\_SIR\_GOP\_2\_\_20110517T085332\_20110517T094311\_C001.HDR
  - CS\_LTA\_\_SIR\_GOP\_2\_\_20110517T085332\_20110517T094311\_C001.nc

This is true for all products and to be done analogously.

## Defined Classes for CryoSat-2

### Class &cryosat2;product

The &cryosat2;product class identifies the gzipped tarballs based on file name matching rules. It represents the base class for all CryoSat-2 products. This class must match the class name referenced by the metadata extractor in the CryoSat-2 addon (separate Java package). 

### Class &cryosat2;productXmlHeader

The &cryosat2;productXmlHeader class identifies the XML header. This is an auxiliary class not directly required for ingesting the product, but for enabling the exploration of the XML header when using the visual product viewer in DHuS (lower right panel).

### Class &cryosat2;productNetCDF

The &cryosat2;productNetCDF class identifies the netCDF files. Like for the XML file, this class enables navigating the netCDF file.

### Class &cryosat2;productFdmDbl

By using the SDF (Structured Data File) implementation for the the DRB (Data Request Broker) it is possible to break down binary files into a tree of nodes on the basis of XML schema definitions. The &cryosat2;productFdmDbl class identifies the SIR\_FDM\_2\_ DBL file and uses SDF to decode the binary file. The location of the XSD needs to be specified for this to work.  
