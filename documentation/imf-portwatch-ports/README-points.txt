PortWatch v2 ports point layer - coordinates used for the atmospheric / environmental point extracts
(source 14 NASA POWER hourly point; source 12 Copernicus ERA5 - see documentation/nasa-power and documentation/copernicus-era5)

Source layer  : IMF PortWatch_ports FeatureServer/1  (ArcGIS REST, read-only query)
Service root  : https://services9.arcgis.com/weJ1QsnbMYJlCHdG/arcgis/rest/services/PortWatch_ports/FeatureServer/1
Query used    : .../FeatureServer/1/query?where=portid+IN+('port514','port1237','port1179')&outFields=portid,portname,lat,long,LOCODE,portclass&returnGeometry=false&f=json
Result        : HTTP 200, 1458 bytes, 3 features returned, exceededTransferLimit not set
Raw response  : portwatchports_v2_port_points_idn.json (this directory) - source-native bytes, not modified

portid	portname	lat	long	LOCODE	portclass	used_as_extract_folder
port514	Jakarta	-6.100544626576063	106.90548758886754		SIP	hourly-point/tanjung-priok/
port1237	Surabaya	-7.200873408963196	112.72166747072802	ID SUB	SIP	hourly-point/surabaya/
port1179	Semarang	-6.949628052109301	110.42412864005829	ID SRG	SIP	hourly-point/semarang/

Field notes (audit trail)
- lat and long above are the decimal values of the response attributes, reproduced with Python repr() so no
  digits are lost or rounded. The response stores them as JSON numbers, not strings.
- LOCODE is null in the response for port514 (empty column above); it is 'ID SUB' for port1237 and
  'ID SRG' for port1179. Not edited.
- spatialReference of the layer geometry is wkid 102100 / latestWkid 3857 (Web Mercator). That applies to the
  GEOMETRY only; returnGeometry=false was used, so no geometry was requested or reprojected. lat and long are
  plain attribute columns already in decimal degrees (WGS84), exactly as the provider publishes them.

Provider naming vs. the extract folder slug
- port514 is named "Jakarta" by PortWatch and has no LOCODE. It is the Greater Jakarta port point that the
  NASA POWER folder is slugged 'tanjung-priok' after, following the parent task assignment. The provider string
  "Jakarta" is NOT rewritten in any stored file; the slug only names the local directory.
- No coordinate was moved, snapped, averaged or rounded. NASA POWER requests used exactly the lat/long above.
