<!doctype html>
<html lang="en">
    <head>
        <meta charset="utf-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="initial-scale=1,user-scalable=no,maximum-scale=1,width=device-width">
        <meta name="mobile-web-app-capable" content="yes">
        <meta name="apple-mobile-web-app-capable" content="yes">
        <link rel="stylesheet" href="css/leaflet.css">
        <link rel="stylesheet" href="css/qgis2web.css"><link rel="stylesheet" href="css/fontawesome-all.min.css">
        <link rel="stylesheet" href="css/filter.css">
<link rel="stylesheet" href="css/nouislider.min.css">
        <link rel="stylesheet" href="css/leaflet-control-geocoder.Geocoder.css">
        <link rel="stylesheet" href="css/leaflet-measure.css">
        <style>
        #map {
            width: 950px;
            height: 579px;
        }
        </style>
        <title>GEO641 Vulnerability Index</title>
    </head>
    <body>
<h2 style="font-family:verdana;">Toronto Vulnerabilty Index Map</h2>

<p style="font-family:verdana;">This map illustrates the safety score of each neighbourhood within the City of Toronto.</p>

<p style="font-family:verdana;">The safety score in this map is based on the combined risk of total assaults and robberies,</p>
<p style="font-family:verdana;">along with the household income and population density of each neighbourhood from 2014.</p>

        <div id="map">
        </div>

<p>Author: Greg Drozdiuk. Data Source: WellbeingToronto City of Toronto © 2018. All Rights Reserved. © OpenStreetMap contributors: Contains data from GeoBase®, GeoGratis (© Department of Natural Resources Canada), CanVec (© Department of Natural Resources Canada), and StatCan (Geography Division, Statistics Canada). Neighbourhood boundaries file was provided by Scholars Portal. Scholars Portal is a service of the Ontario Council of University Libraries (OCUL).

        <script src="js/qgis2web_expressions.js"></script>
        <script src="js/leaflet.js"></script>
        <script src="js/leaflet.rotatedMarker.js"></script>
        <script src="js/leaflet.pattern.js"></script>
        <script src="js/leaflet-hash.js"></script>
        <script src="js/Autolinker.min.js"></script>
        <script src="js/rbush.min.js"></script>
        <script src="js/labelgun.min.js"></script>
        <script src="js/labels.js"></script>
        <script src="js/leaflet-control-geocoder.Geocoder.js"></script>
        <script src="js/leaflet-measure.js"></script>
        <script src="js/tailDT.js"></script>
<script src="js/nouislider.min.js"></script>
<script src="js/wNumb.js"></script>
        <script src="data/PopulationDensity_1.js"></script>
        <script src="data/HouseholdIncome_2.js"></script>
        <script src="data/Assaults_3.js"></script>
        <script src="data/Robberies_4.js"></script>
        <script src="data/IndexScore_5.js"></script>
        <script>
        var highlightLayer;
        function highlightFeature(e) {
            highlightLayer = e.target;

            if (e.target.feature.geometry.type === 'LineString') {
              highlightLayer.setStyle({
                color: '#ffff00',
              });
            } else {
              highlightLayer.setStyle({
                fillColor: '#ffff00',
                fillOpacity: 1
              });
            }
        }
        var map = L.map('map', {
            zoomControl:true, maxZoom:28, minZoom:1
        }).fitBounds([[43.55443862176433,-79.70105959280751],[43.871997617643274,-78.91439059883872]]);
        var hash = new L.Hash(map);
        map.attributionControl.setPrefix('<a href="https://github.com/tomchadwin/qgis2web" target="_blank">qgis2web</a> &middot; <a href="https://leafletjs.com" title="A JS library for interactive maps">Leaflet</a> &middot; <a href="https://qgis.org">QGIS</a>');
        var autolinker = new Autolinker({truncate: {length: 30, location: 'smart'}});
        var measureControl = new L.Control.Measure({
            position: 'topleft',
            primaryLengthUnit: 'meters',
            secondaryLengthUnit: 'kilometers',
            primaryAreaUnit: 'sqmeters',
            secondaryAreaUnit: 'hectares'
        });
        measureControl.addTo(map);
        document.getElementsByClassName('leaflet-control-measure-toggle')[0]
        .innerHTML = '';
        document.getElementsByClassName('leaflet-control-measure-toggle')[0]
        .className += ' fas fa-ruler';
        var bounds_group = new L.featureGroup([]);
        function setBounds() {
        }
        map.createPane('pane_OSMStandard_0');
        map.getPane('pane_OSMStandard_0').style.zIndex = 400;
        var layer_OSMStandard_0 = L.tileLayer('http://tile.openstreetmap.org/{z}/{x}/{y}.png', {
            pane: 'pane_OSMStandard_0',
            opacity: 1.0,
            attribution: '<a href="https://www.openstreetmap.org/copyright">© OpenStreetMap contributors, CC-BY-SA</a>',
            minZoom: 1,
            maxZoom: 28,
            minNativeZoom: 0,
            maxNativeZoom: 19
        });
        layer_OSMStandard_0;
        map.addLayer(layer_OSMStandard_0);
        function pop_PopulationDensity_1(feature, layer) {
            layer.on({
                mouseout: function(e) {
                    for (i in e.target._eventParents) {
                        e.target._eventParents[i].resetStyle(e.target);
                    }
                },
                mouseover: highlightFeature,
            });
            var popupContent = '<table>\
                    <tr>\
                        <td colspan="2">' + (feature.properties['Name'] !== null ? autolinker.link(feature.properties['Name'].toLocaleString()) : '') + '</td>\
                    </tr>\
                    <tr>\
                        <td colspan="2">' + (feature.properties['VI1 Sheet1'] !== null ? autolinker.link(feature.properties['VI1 Sheet1'].toLocaleString()) : '') + '</td>\
                    </tr>\
                    <tr>\
                        <th scope="row">Density</th>\
                        <td>' + (feature.properties['Density'] !== null ? autolinker.link(feature.properties['Density'].toLocaleString()) : '') + '</td>\
                    </tr>\
                    <tr>\
                        <th scope="row">Robbery</th>\
                        <td>' + (feature.properties['Robbery'] !== null ? autolinker.link(feature.properties['Robbery'].toLocaleString()) : '') + '</td>\
                    </tr>\
                    <tr>\
                        <th scope="row">Assault</th>\
                        <td>' + (feature.properties['Assault'] !== null ? autolinker.link(feature.properties['Assault'].toLocaleString()) : '') + '</td>\
                    </tr>\
                    <tr>\
                        <th scope="row">Score</th>\
                        <td>' + (feature.properties['Score'] !== null ? autolinker.link(feature.properties['Score'].toLocaleString()) : '') + '</td>\
                    </tr>\
                    <th scope="row">Household Income</th>\
                        <td>' + (feature.properties['Household Income'] !== null ? autolinker.link(feature.properties['VI2 Vulnerability Indicators_SRincome'].toLocaleString()) : '') + '</td>\
                    </tr>\
                </table>';
            layer.bindPopup(popupContent, {maxHeight: 400});
        }

        function style_PopulationDensity_1_0(feature) {
            if (feature.properties['Density'] >= 0.000000 && feature.properties['Density'] <= 0.046990 ) {
                return {
                pane: 'pane_PopulationDensity_1',
                opacity: 1,
                color: 'rgba(35,35,35,0.33)',
                dashArray: '',
                lineCap: 'butt',
                lineJoin: 'miter',
                weight: 1.0, 
                fill: true,
                fillOpacity: 1,
                fillColor: 'rgba(255,255,255,0.33)',
                interactive: false,
            }
            }
            if (feature.properties['Density'] >= 0.046990 && feature.properties['Density'] <= 0.072326 ) {
                return {
                pane: 'pane_PopulationDensity_1',
                opacity: 1,
                color: 'rgba(35,35,35,0.33)',
                dashArray: '',
                lineCap: 'butt',
                lineJoin: 'miter',
                weight: 1.0, 
                fill: true,
                fillOpacity: 1,
                fillColor: 'rgba(255,191,191,0.33)',
                interactive: false,
            }
            }
            if (feature.properties['Density'] >= 0.072326 && feature.properties['Density'] <= 0.114175 ) {
                return {
                pane: 'pane_PopulationDensity_1',
                opacity: 1,
                color: 'rgba(35,35,35,0.33)',
                dashArray: '',
                lineCap: 'butt',
                lineJoin: 'miter',
                weight: 1.0, 
                fill: true,
                fillOpacity: 1,
                fillColor: 'rgba(255,128,128,0.33)',
                interactive: false,
            }
            }
            if (feature.properties['Density'] >= 0.114175 && feature.properties['Density'] <= 0.160464 ) {
                return {
                pane: 'pane_PopulationDensity_1',
                opacity: 1,
                color: 'rgba(35,35,35,0.33)',
                dashArray: '',
                lineCap: 'butt',
                lineJoin: 'miter',
                weight: 1.0, 
                fill: true,
                fillOpacity: 1,
                fillColor: 'rgba(255,64,64,0.33)',
                interactive: false,
            }
            }
            if (feature.properties['Density'] >= 0.160464 && feature.properties['Density'] <= 1.000000 ) {
                return {
                pane: 'pane_PopulationDensity_1',
                opacity: 1,
                color: 'rgba(35,35,35,0.33)',
                dashArray: '',
                lineCap: 'butt',
                lineJoin: 'miter',
                weight: 1.0, 
                fill: true,
                fillOpacity: 1,
                fillColor: 'rgba(255,0,0,0.33)',
                interactive: false,
            }
            }
        }
        map.createPane('pane_PopulationDensity_1');
        map.getPane('pane_PopulationDensity_1').style.zIndex = 401;
        map.getPane('pane_PopulationDensity_1').style['mix-blend-mode'] = 'normal';
        var layer_PopulationDensity_1 = new L.geoJson(json_PopulationDensity_1, {
            attribution: '',
            interactive: false,
            dataVar: 'json_PopulationDensity_1',
            layerName: 'layer_PopulationDensity_1',
            pane: 'pane_PopulationDensity_1',
            onEachFeature: pop_PopulationDensity_1,
            style: style_PopulationDensity_1_0,
        });
        bounds_group.addLayer(layer_PopulationDensity_1);
        map.addLayer(layer_PopulationDensity_1);
        function pop_HouseholdIncome_2(feature, layer) {
            layer.on({
                mouseout: function(e) {
                    for (i in e.target._eventParents) {
                        e.target._eventParents[i].resetStyle(e.target);
                    }
                },
                mouseover: highlightFeature,
            });
            var popupContent = '<table>\
                    <tr>\
                        <td colspan="2">' + (feature.properties['VI1 Sheet1'] !== null ? autolinker.link(feature.properties['VI1 Sheet1'].toLocaleString()) : '') + '</td>\
                    </tr>\
                    <tr>\
                        <th scope="row">Density</th>\
                        <td>' + (feature.properties['Density'] !== null ? autolinker.link(feature.properties['Density'].toLocaleString()) : '') + '</td>\
                    </tr>\
                    <tr>\
                        <th scope="row">Robbery</th>\
                        <td>' + (feature.properties['Robbery'] !== null ? autolinker.link(feature.properties['Robbery'].toLocaleString()) : '') + '</td>\
                    </tr>\
                    <tr>\
                        <th scope="row">Assault</th>\
                        <td>' + (feature.properties['Assault'] !== null ? autolinker.link(feature.properties['Assault'].toLocaleString()) : '') + '</td>\
                    </tr>\
                    <tr>\
                        <th scope="row">Score</th>\
                        <td>' + (feature.properties['Score'] !== null ? autolinker.link(feature.properties['Score'].toLocaleString()) : '') + '</td>\
                    </tr>\
                    <tr>\
                        <th scope="row">Household Income</th>\
                        <td>' + (feature.properties['VI2 Vulnerability Indicators_SRincome'] !== null ? autolinker.link(feature.properties['VI2 Vulnerability Indicators_SRincome'].toLocaleString()) : '') + '</td>\
                    </tr>\
                </table>';
            layer.bindPopup(popupContent, {maxHeight: 400});
        }

        function style_HouseholdIncome_2_0(feature) {
            if (feature.properties['VI2 Vulnerability Indicators_SRincome'] >= 0.000000 && feature.properties['VI2 Vulnerability Indicators_SRincome'] <= 0.050333 ) {
                return {
                pane: 'pane_HouseholdIncome_2',
                opacity: 1,
                color: 'rgba(35,35,35,0.33)',
                dashArray: '',
                lineCap: 'butt',
                lineJoin: 'miter',
                weight: 1.0, 
                fill: true,
                fillOpacity: 1,
                fillColor: 'rgba(255,0,0,0.33)',
                interactive: false,
            }
            }
            if (feature.properties['VI2 Vulnerability Indicators_SRincome'] >= 0.050333 && feature.properties['VI2 Vulnerability Indicators_SRincome'] <= 0.068841 ) {
                return {
                pane: 'pane_HouseholdIncome_2',
                opacity: 1,
                color: 'rgba(35,35,35,0.33)',
                dashArray: '',
                lineCap: 'butt',
                lineJoin: 'miter',
                weight: 1.0, 
                fill: true,
                fillOpacity: 1,
                fillColor: 'rgba(255,64,64,0.33)',
                interactive: false,
            }
            }
            if (feature.properties['VI2 Vulnerability Indicators_SRincome'] >= 0.068841 && feature.properties['VI2 Vulnerability Indicators_SRincome'] <= 0.113895 ) {
                return {
                pane: 'pane_HouseholdIncome_2',
                opacity: 1,
                color: 'rgba(35,35,35,0.33)',
                dashArray: '',
                lineCap: 'butt',
                lineJoin: 'miter',
                weight: 1.0, 
                fill: true,
                fillOpacity: 1,
                fillColor: 'rgba(255,128,128,0.33)',
                interactive: false,
            }
            }
            if (feature.properties['VI2 Vulnerability Indicators_SRincome'] >= 0.113895 && feature.properties['VI2 Vulnerability Indicators_SRincome'] <= 0.169425 ) {
                return {
                pane: 'pane_HouseholdIncome_2',
                opacity: 1,
                color: 'rgba(35,35,35,0.33)',
                dashArray: '',
                lineCap: 'butt',
                lineJoin: 'miter',
                weight: 1.0, 
                fill: true,
                fillOpacity: 1,
                fillColor: 'rgba(255,191,191,0.33)',
                interactive: false,
            }
            }
            if (feature.properties['VI2 Vulnerability Indicators_SRincome'] >= 0.169425 && feature.properties['VI2 Vulnerability Indicators_SRincome'] <= 1.000000 ) {
                return {
                pane: 'pane_HouseholdIncome_2',
                opacity: 1,
                color: 'rgba(35,35,35,0.33)',
                dashArray: '',
                lineCap: 'butt',
                lineJoin: 'miter',
                weight: 1.0, 
                fill: true,
                fillOpacity: 1,
                fillColor: 'rgba(255,255,255,0.33)',
                interactive: false,
            }
            }
        }
        map.createPane('pane_HouseholdIncome_2');
        map.getPane('pane_HouseholdIncome_2').style.zIndex = 402;
        map.getPane('pane_HouseholdIncome_2').style['mix-blend-mode'] = 'normal';
        var layer_HouseholdIncome_2 = new L.geoJson(json_HouseholdIncome_2, {
            attribution: '',
            interactive: false,
            dataVar: 'json_HouseholdIncome_2',
            layerName: 'layer_HouseholdIncome_2',
            pane: 'pane_HouseholdIncome_2',
            onEachFeature: pop_HouseholdIncome_2,
            style: style_HouseholdIncome_2_0,
        });
        bounds_group.addLayer(layer_HouseholdIncome_2);
        map.addLayer(layer_HouseholdIncome_2);
        function pop_Assaults_3(feature, layer) {
            layer.on({
                mouseout: function(e) {
                    for (i in e.target._eventParents) {
                        e.target._eventParents[i].resetStyle(e.target);
                    }
                },
                mouseover: highlightFeature,
            });
            var popupContent = '<table>\
                    </tr>\
                    <tr>\
                        <td colspan="2">' + (feature.properties['VI1 Sheet1'] !== null ? autolinker.link(feature.properties['VI1 Sheet1'].toLocaleString()) : '') + '</td>\
                    </tr>\
                    <tr>\
                        <th scope="row">Density</th>\
                        <td>' + (feature.properties['Density'] !== null ? autolinker.link(feature.properties['Density'].toLocaleString()) : '') + '</td>\
                    </tr>\
                    <tr>\
                        <th scope="row">Robbery</th>\
                        <td>' + (feature.properties['Robbery'] !== null ? autolinker.link(feature.properties['Robbery'].toLocaleString()) : '') + '</td>\
                    </tr>\
                    <tr>\
                        <th scope="row">Assault</th>\
                        <td>' + (feature.properties['Assault'] !== null ? autolinker.link(feature.properties['Assault'].toLocaleString()) : '') + '</td>\
                    </tr>\
                    <tr>\
                        <th scope="row">Score</th>\
                        <td>' + (feature.properties['Score'] !== null ? autolinker.link(feature.properties['Score'].toLocaleString()) : '') + '</td>\
                    </tr>\
                    <tr>\
                        <th scope="row">Household Income</th>\
                        <td>' + (feature.properties['VI2 Vulnerability Indicators_SRincome'] !== null ? autolinker.link(feature.properties['VI2 Vulnerability Indicators_SRincome'].toLocaleString()) : '') + '</td>\
                    </tr>\
                </table>';
            layer.bindPopup(popupContent, {maxHeight: 400});
        }

        function style_Assaults_3_0(feature) {
            if (feature.properties['Assault'] >= 0.000000 && feature.properties['Assault'] <= 0.200000 ) {
                return {
                pane: 'pane_Assaults_3',
                opacity: 1,
                color: 'rgba(35,35,35,0.33)',
                dashArray: '',
                lineCap: 'butt',
                lineJoin: 'miter',
                weight: 1.0, 
                fill: true,
                fillOpacity: 1,
                fillColor: 'rgba(255,255,255,0.33)',
                interactive: false,
            }
            }
            if (feature.properties['Assault'] >= 0.200000 && feature.properties['Assault'] <= 0.400000 ) {
                return {
                pane: 'pane_Assaults_3',
                opacity: 1,
                color: 'rgba(35,35,35,0.33)',
                dashArray: '',
                lineCap: 'butt',
                lineJoin: 'miter',
                weight: 1.0, 
                fill: true,
                fillOpacity: 1,
                fillColor: 'rgba(255,191,191,0.33)',
                interactive: false,
            }
            }
            if (feature.properties['Assault'] >= 0.400000 && feature.properties['Assault'] <= 0.600000 ) {
                return {
                pane: 'pane_Assaults_3',
                opacity: 1,
                color: 'rgba(35,35,35,0.33)',
                dashArray: '',
                lineCap: 'butt',
                lineJoin: 'miter',
                weight: 1.0, 
                fill: true,
                fillOpacity: 1,
                fillColor: 'rgba(255,128,128,0.33)',
                interactive: false,
            }
            }
            if (feature.properties['Assault'] >= 0.600000 && feature.properties['Assault'] <= 0.800000 ) {
                return {
                pane: 'pane_Assaults_3',
                opacity: 1,
                color: 'rgba(35,35,35,0.33)',
                dashArray: '',
                lineCap: 'butt',
                lineJoin: 'miter',
                weight: 1.0, 
                fill: true,
                fillOpacity: 1,
                fillColor: 'rgba(255,64,64,0.33)',
                interactive: false,
            }
            }
            if (feature.properties['Assault'] >= 0.800000 && feature.properties['Assault'] <= 1.000000 ) {
                return {
                pane: 'pane_Assaults_3',
                opacity: 1,
                color: 'rgba(35,35,35,0.33)',
                dashArray: '',
                lineCap: 'butt',
                lineJoin: 'miter',
                weight: 1.0, 
                fill: true,
                fillOpacity: 1,
                fillColor: 'rgba(255,0,0,0.33)',
                interactive: false,
            }
            }
        }
        map.createPane('pane_Assaults_3');
        map.getPane('pane_Assaults_3').style.zIndex = 403;
        map.getPane('pane_Assaults_3').style['mix-blend-mode'] = 'normal';
        var layer_Assaults_3 = new L.geoJson(json_Assaults_3, {
            attribution: '',
            interactive: false,
            dataVar: 'json_Assaults_3',
            layerName: 'layer_Assaults_3',
            pane: 'pane_Assaults_3',
            onEachFeature: pop_Assaults_3,
            style: style_Assaults_3_0,
        });
        bounds_group.addLayer(layer_Assaults_3);
        map.addLayer(layer_Assaults_3);
        function pop_Robberies_4(feature, layer) {
            layer.on({
                mouseout: function(e) {
                    for (i in e.target._eventParents) {
                        e.target._eventParents[i].resetStyle(e.target);
                    }
                },
                mouseover: highlightFeature,
            });
            var popupContent = '<table>\
                    <tr>\
                        <td colspan="2">' + (feature.properties['VI1 Sheet1'] !== null ? autolinker.link(feature.properties['VI1 Sheet1'].toLocaleString()) : '') + '</td>\
                    </tr>\
                    <tr>\
                        <th scope="row">Density</th>\
                        <td>' + (feature.properties['Density'] !== null ? autolinker.link(feature.properties['Density'].toLocaleString()) : '') + '</td>\
                    </tr>\
                    <tr>\
                        <th scope="row">Robbery</th>\
                        <td>' + (feature.properties['Robbery'] !== null ? autolinker.link(feature.properties['Robbery'].toLocaleString()) : '') + '</td>\
                    </tr>\
                    <tr>\
                        <th scope="row">Assault</th>\
                        <td>' + (feature.properties['Assault'] !== null ? autolinker.link(feature.properties['Assault'].toLocaleString()) : '') + '</td>\
                    </tr>\
                    <tr>\
                        <th scope="row">Score</th>\
                        <td>' + (feature.properties['Score'] !== null ? autolinker.link(feature.properties['Score'].toLocaleString()) : '') + '</td>\
                    </tr>\
                    <tr>\
                        <th scope="row">Household Income</th>\
                        <td>' + (feature.properties['VI2 Vulnerability Indicators_SRincome'] !== null ? autolinker.link(feature.properties['VI2 Vulnerability Indicators_SRincome'].toLocaleString()) : '') + '</td>\
                    </tr>\
                </table>';
            layer.bindPopup(popupContent, {maxHeight: 400});
        }

        function style_Robberies_4_0(feature) {
            if (feature.properties['Robbery'] >= 0.000000 && feature.properties['Robbery'] <= 0.200000 ) {
                return {
                pane: 'pane_Robberies_4',
                opacity: 1,
                color: 'rgba(35,35,35,0.33)',
                dashArray: '',
                lineCap: 'butt',
                lineJoin: 'miter',
                weight: 1.0, 
                fill: true,
                fillOpacity: 1,
                fillColor: 'rgba(255,255,255,0.33)',
                interactive: false,
            }
            }
            if (feature.properties['Robbery'] >= 0.200000 && feature.properties['Robbery'] <= 0.400000 ) {
                return {
                pane: 'pane_Robberies_4',
                opacity: 1,
                color: 'rgba(35,35,35,0.33)',
                dashArray: '',
                lineCap: 'butt',
                lineJoin: 'miter',
                weight: 1.0, 
                fill: true,
                fillOpacity: 1,
                fillColor: 'rgba(255,191,191,0.33)',
                interactive: false,
            }
            }
            if (feature.properties['Robbery'] >= 0.400000 && feature.properties['Robbery'] <= 0.600000 ) {
                return {
                pane: 'pane_Robberies_4',
                opacity: 1,
                color: 'rgba(35,35,35,0.33)',
                dashArray: '',
                lineCap: 'butt',
                lineJoin: 'miter',
                weight: 1.0, 
                fill: true,
                fillOpacity: 1,
                fillColor: 'rgba(255,128,128,0.33)',
                interactive: false,
            }
            }
            if (feature.properties['Robbery'] >= 0.600000 && feature.properties['Robbery'] <= 0.800000 ) {
                return {
                pane: 'pane_Robberies_4',
                opacity: 1,
                color: 'rgba(35,35,35,0.33)',
                dashArray: '',
                lineCap: 'butt',
                lineJoin: 'miter',
                weight: 1.0, 
                fill: true,
                fillOpacity: 1,
                fillColor: 'rgba(255,64,64,0.33)',
                interactive: false,
            }
            }
            if (feature.properties['Robbery'] >= 0.800000 && feature.properties['Robbery'] <= 1.000000 ) {
                return {
                pane: 'pane_Robberies_4',
                opacity: 1,
                color: 'rgba(35,35,35,0.33)',
                dashArray: '',
                lineCap: 'butt',
                lineJoin: 'miter',
                weight: 1.0, 
                fill: true,
                fillOpacity: 1,
                fillColor: 'rgba(255,0,0,0.33)',
                interactive: false,
            }
            }
        }
        map.createPane('pane_Robberies_4');
        map.getPane('pane_Robberies_4').style.zIndex = 404;
        map.getPane('pane_Robberies_4').style['mix-blend-mode'] = 'normal';
        var layer_Robberies_4 = new L.geoJson(json_Robberies_4, {
            attribution: '',
            interactive: false,
            dataVar: 'json_Robberies_4',
            layerName: 'layer_Robberies_4',
            pane: 'pane_Robberies_4',
            onEachFeature: pop_Robberies_4,
            style: style_Robberies_4_0,
        });
        bounds_group.addLayer(layer_Robberies_4);
        map.addLayer(layer_Robberies_4);
        function pop_IndexScore_5(feature, layer) {
            layer.on({
                mouseout: function(e) {
                    for (i in e.target._eventParents) {
                        e.target._eventParents[i].resetStyle(e.target);
                    }
                },
                mouseover: highlightFeature,
            });
            var popupContent = '<table>\
                    <tr>\
                        <td colspan="2">' + (feature.properties['ID'] !== null ? autolinker.link(feature.properties['ID'].toLocaleString()) : '') + '</td>\
                    </tr>\
                    <tr>\
                        <td colspan="2">' + (feature.properties['Name'] !== null ? autolinker.link(feature.properties['Name'].toLocaleString()) : '') + '</td>\
                    </tr>\
                    <tr>\
                        <td colspan="2">' + (feature.properties['VI1 Sheet1'] !== null ? autolinker.link(feature.properties['VI1 Sheet1'].toLocaleString()) : '') + '</td>\
                    </tr>\
                    <tr>\
                        <th scope="row">Density</th>\
                        <td>' + (feature.properties['Density'] !== null ? autolinker.link(feature.properties['Density'].toLocaleString()) : '') + '</td>\
                    </tr>\
                    <tr>\
                        <th scope="row">Robbery</th>\
                        <td>' + (feature.properties['Robbery'] !== null ? autolinker.link(feature.properties['Robbery'].toLocaleString()) : '') + '</td>\
                    </tr>\
                    <tr>\
                        <th scope="row">Assault</th>\
                        <td>' + (feature.properties['Assault'] !== null ? autolinker.link(feature.properties['Assault'].toLocaleString()) : '') + '</td>\
                    </tr>\
                    <tr>\
                        <th scope="row">Score</th>\
                        <td>' + (feature.properties['Score'] !== null ? autolinker.link(feature.properties['Score'].toLocaleString()) : '') + '</td>\
                    </tr>\
                    <tr>\
                        <th scope="row">Household Income</th>\
                        <td>' + (feature.properties['VI2 Vulnerability Indicators_SRincome'] !== null ? autolinker.link(feature.properties['VI2 Vulnerability Indicators_SRincome'].toLocaleString()) : '') + '</td>\
                    </tr>\
                </table>';
            layer.bindPopup(popupContent, {maxHeight: 400});
        }

        function style_IndexScore_5_0(feature) {
            if (feature.properties['Score'] >= 0.066000 && feature.properties['Score'] <= 0.112800 ) {
                return {
                pane: 'pane_IndexScore_5',
                opacity: 1,
                color: 'rgba(35,35,35,1.0)',
                dashArray: '',
                lineCap: 'butt',
                lineJoin: 'miter',
                weight: 1.0, 
                fill: true,
                fillOpacity: 1,
                fillColor: 'rgba(255,255,255,1.0)',
                interactive: true,
            }
            }
            if (feature.properties['Score'] >= 0.112800 && feature.properties['Score'] <= 0.146800 ) {
                return {
                pane: 'pane_IndexScore_5',
                opacity: 1,
                color: 'rgba(35,35,35,1.0)',
                dashArray: '',
                lineCap: 'butt',
                lineJoin: 'miter',
                weight: 1.0, 
                fill: true,
                fillOpacity: 1,
                fillColor: 'rgba(255,191,191,1.0)',
                interactive: true,
            }
            }
            if (feature.properties['Score'] >= 0.146800 && feature.properties['Score'] <= 0.186200 ) {
                return {
                pane: 'pane_IndexScore_5',
                opacity: 1,
                color: 'rgba(35,35,35,1.0)',
                dashArray: '',
                lineCap: 'butt',
                lineJoin: 'miter',
                weight: 1.0, 
                fill: true,
                fillOpacity: 1,
                fillColor: 'rgba(255,128,128,1.0)',
                interactive: true,
            }
            }
            if (feature.properties['Score'] >= 0.186200 && feature.properties['Score'] <= 0.229600 ) {
                return {
                pane: 'pane_IndexScore_5',
                opacity: 1,
                color: 'rgba(35,35,35,1.0)',
                dashArray: '',
                lineCap: 'butt',
                lineJoin: 'miter',
                weight: 1.0, 
                fill: true,
                fillOpacity: 1,
                fillColor: 'rgba(255,64,64,1.0)',
                interactive: true,
            }
            }
            if (feature.properties['Score'] >= 0.229600 && feature.properties['Score'] <= 0.726000 ) {
                return {
                pane: 'pane_IndexScore_5',
                opacity: 1,
                color: 'rgba(35,35,35,1.0)',
                dashArray: '',
                lineCap: 'butt',
                lineJoin: 'miter',
                weight: 1.0, 
                fill: true,
                fillOpacity: 1,
                fillColor: 'rgba(255,0,0,1.0)',
                interactive: true,
            }
            }
        }
        map.createPane('pane_IndexScore_5');
        map.getPane('pane_IndexScore_5').style.zIndex = 405;
        map.getPane('pane_IndexScore_5').style['mix-blend-mode'] = 'normal';
        var layer_IndexScore_5 = new L.geoJson(json_IndexScore_5, {
            attribution: '',
            interactive: true,
            dataVar: 'json_IndexScore_5',
            layerName: 'layer_IndexScore_5',
            pane: 'pane_IndexScore_5',
            onEachFeature: pop_IndexScore_5,
            style: style_IndexScore_5_0,
        });
        bounds_group.addLayer(layer_IndexScore_5);
        map.addLayer(layer_IndexScore_5);
        var osmGeocoder = new L.Control.Geocoder({
            collapsed: true,
            position: 'topleft',
            text: 'Search',
            title: 'Testing'
        }).addTo(map);
        document.getElementsByClassName('leaflet-control-geocoder-icon')[0]
        .className += ' fa fa-search';
        document.getElementsByClassName('leaflet-control-geocoder-icon')[0]
        .title += 'Search for a place';
        var baseMaps = {};
        L.control.layers(baseMaps,{'Index Score<br /><table><tr><td style="text-align: center;"><img src="legend/IndexScore_5_006601130.png" /></td><td>0.066 - 0.113</td></tr><tr><td style="text-align: center;"><img src="legend/IndexScore_5_011301471.png" /></td><td>0.113 - 0.147</td></tr><tr><td style="text-align: center;"><img src="legend/IndexScore_5_014701862.png" /></td><td>0.147 - 0.186</td></tr><tr><td style="text-align: center;"><img src="legend/IndexScore_5_01860233.png" /></td><td>0.186 - 0.23</td></tr><tr><td style="text-align: center;"><img src="legend/IndexScore_5_02307264.png" /></td><td>0.23 - 0.726</td></tr></table><hr>': layer_IndexScore_5,'Robberies<br />': layer_Robberies_4,'Assaults<br /><table><tr><td style="text-align: center;"><img src="legend/Assaults_3_0020.png" /></td><td>0 - 0.2</td></tr><tr><td style="text-align: center;"><img src="legend/Assaults_3_02041.png" /></td><td>0.2 - 0.4</td></tr><tr><td style="text-align: center;"><img src="legend/Assaults_3_04062.png" /></td><td>0.4 - 0.6</td></tr><tr><td style="text-align: center;"><img src="legend/Assaults_3_06083.png" /></td><td>0.6 - 0.8</td></tr><tr><td style="text-align: center;"><img src="legend/Assaults_3_0814.png" /></td><td>0.8 - 1</td></tr></table>': layer_Assaults_3,'Household Income<br /><table><tr><td style="text-align: center;"><img src="legend/HouseholdIncome_2_00050.png" /></td><td>0 - 0.05</td></tr><tr><td style="text-align: center;"><img src="legend/HouseholdIncome_2_00500691.png" /></td><td>0.05 - 0.069</td></tr><tr><td style="text-align: center;"><img src="legend/HouseholdIncome_2_006901142.png" /></td><td>0.069 - 0.114</td></tr><tr><td style="text-align: center;"><img src="legend/HouseholdIncome_2_011401693.png" /></td><td>0.114 - 0.169</td></tr><tr><td style="text-align: center;"><img src="legend/HouseholdIncome_2_016914.png" /></td><td>0.169 - 1</td></tr></table>': layer_HouseholdIncome_2,'Population Density<br /><table><tr><td style="text-align: center;"><img src="legend/PopulationDensity_1_000470.png" /></td><td>0 - 0.047</td></tr><tr><td style="text-align: center;"><img src="legend/PopulationDensity_1_004700721.png" /></td><td>0.047 - 0.072</td></tr><tr><td style="text-align: center;"><img src="legend/PopulationDensity_1_007201142.png" /></td><td>0.072 - 0.114</td></tr><tr><td style="text-align: center;"><img src="legend/PopulationDensity_1_01140163.png" /></td><td>0.114 - 0.16</td></tr><tr><td style="text-align: center;"><img src="legend/PopulationDensity_1_01614.png" /></td><td>0.16 - 1</td></tr></table>': layer_PopulationDensity_1,"OSM Standard": layer_OSMStandard_0,},{collapsed:false}).addTo(map);
        setBounds();
        var mapDiv = document.getElementById('map');
        var row = document.createElement('div');
        row.className="row";
        row.id="all";
        row.style.height = "100%";
        var col1 = document.createElement('div');
        col1.className="col9";
        col1.id = "mapWindow";
        col1.style.height = "99%";
        col1.style.width = "80%";
        col1.style.display = "inline-block";
        var col2 = document.createElement('div');
        col2.className="col3";
        col2.id = "menu";
        col2.style.display = "inline-block";
        mapDiv.parentNode.insertBefore(row, mapDiv);
        document.getElementById("all").appendChild(col1);
        document.getElementById("all").appendChild(col2);
        col1.appendChild(mapDiv)
        var Filters = {"VI1 Sheet1": "str"};
        function filterFunc() {
          map.eachLayer(function(lyr){
          if ("options" in lyr && "dataVar" in lyr["options"]){
            features = this[lyr["options"]["dataVar"]].features.slice(0);
            try{
              for (key in Filters){
                keyS = key.replace(/[^a-zA-Z0-9_]/g, "")
                if (Filters[key] == "str" || Filters[key] == "bool"){
                  var selection = [];
                  var options = document.getElementById("sel_" + keyS).options
                  for (var i=0; i < options.length; i++) {
                    if (options[i].selected) selection.push(options[i].value);
                  }
                    try{
                      if (key in features[0].properties){
                        for (i = features.length - 1;
                          i >= 0; --i){
                          if (selection.indexOf(
                          features[i].properties[key])<0
                          && selection.length>0) {
                          features.splice(i,1);
                          }
                        }
                      }
                    } catch(err){
                  }
                }
                if (Filters[key] == "int"){
                  sliderVals =  document.getElementById(
                    "div_" + keyS).noUiSlider.get();
                  try{
                    if (key in features[0].properties){
                    for (i = features.length - 1; i >= 0; --i){
                      if (parseInt(features[i].properties[key])
                          < sliderVals[0]
                          || parseInt(features[i].properties[key])
                          > sliderVals[1]){
                            features.splice(i,1);
                          }
                        }
                      }
                    } catch(err){
                    }
                  }
                if (Filters[key] == "real"){
                  sliderVals =  document.getElementById(
                    "div_" + keyS).noUiSlider.get();
                  try{
                    if (key in features[0].properties){
                    for (i = features.length - 1; i >= 0; --i){
                      if (features[i].properties[key]
                          < sliderVals[0]
                          || features[i].properties[key]
                          > sliderVals[1]){
                            features.splice(i,1);
                          }
                        }
                      }
                    } catch(err){
                    }
                  }
                if (Filters[key] == "date"
                  || Filters[key] == "datetime"
                  || Filters[key] == "time"){
                  try{
                    if (key in features[0].properties){
                      HTMLkey = key.replace(/[&\/\\#,+()$~%.'":*?<>{} ]/g, '');
                      startdate = document.getElementById("dat_" +
                        HTMLkey + "_date1").value.replace(" ", "T");
                      enddate = document.getElementById("dat_" +
                        HTMLkey + "_date2").value.replace(" ", "T");
                      for (i = features.length - 1; i >= 0; --i){
                        if (features[i].properties[key] < startdate
                          || features[i].properties[key] > enddate){
                          features.splice(i,1);
                        }
                      }
                    }
                  } catch(err){
                  }
                }
              }
            } catch(err){
            }
          this[lyr["options"]["layerName"]].clearLayers();
          this[lyr["options"]["layerName"]].addData(features);
          }
          })
        }
            document.getElementById("menu").appendChild(
                document.createElement("div"));
            var div_VI1Sheet1 = document.createElement('div');
            div_VI1Sheet1.id = "div_VI1Sheet1";
            div_VI1Sheet1.className= "filterselect";
            document.getElementById("menu").appendChild(div_VI1Sheet1);
            sel_VI1Sheet1 = document.createElement('select');
            sel_VI1Sheet1.multiple = true;
            sel_VI1Sheet1.size = 10;
            sel_VI1Sheet1.id = "sel_VI1Sheet1";
            var VI1Sheet1_options_str = "<option value='' unselected></option>";
            sel_VI1Sheet1.onchange = function(){filterFunc()};
            VI1Sheet1_options_str  += '<option value="Agincourt North">Agincourt North</option>';
            VI1Sheet1_options_str  += '<option value="Agincourt South-Malvern West">Agincourt South-Malvern West</option>';
            VI1Sheet1_options_str  += '<option value="Alderwood">Alderwood</option>';
            VI1Sheet1_options_str  += '<option value="Annex">Annex</option>';
            VI1Sheet1_options_str  += '<option value="Banbury-Don Mills">Banbury-Don Mills</option>';
            VI1Sheet1_options_str  += '<option value="Bathurst Manor">Bathurst Manor</option>';
            VI1Sheet1_options_str  += '<option value="Bay Street Corridor">Bay Street Corridor</option>';
            VI1Sheet1_options_str  += '<option value="Bayview Village">Bayview Village</option>';
            VI1Sheet1_options_str  += '<option value="Bayview Woods-Steeles">Bayview Woods-Steeles</option>';
            VI1Sheet1_options_str  += '<option value="Bedford Park-Nortown">Bedford Park-Nortown</option>';
            VI1Sheet1_options_str  += '<option value="Beechborough-Greenbrook">Beechborough-Greenbrook</option>';
            VI1Sheet1_options_str  += '<option value="Bendale">Bendale</option>';
            VI1Sheet1_options_str  += '<option value="Birchcliffe-Cliffside">Birchcliffe-Cliffside</option>';
            VI1Sheet1_options_str  += '<option value="Black Creek">Black Creek</option>';
            VI1Sheet1_options_str  += '<option value="Blake-Jones">Blake-Jones</option>';
            VI1Sheet1_options_str  += '<option value="Briar Hill-Belgravia">Briar Hill-Belgravia</option>';
            VI1Sheet1_options_str  += '<option value="Bridle Path-Sunnybrook-York Mills">Bridle Path-Sunnybrook-York Mills</option>';
            VI1Sheet1_options_str  += '<option value="Broadview North">Broadview North</option>';
            VI1Sheet1_options_str  += '<option value="Brookhaven-Amesbury">Brookhaven-Amesbury</option>';
            VI1Sheet1_options_str  += '<option value="Cabbagetown-South St.James Town">Cabbagetown-South St.James Town</option>';
            VI1Sheet1_options_str  += '<option value="Caledonia-Fairbank">Caledonia-Fairbank</option>';
            VI1Sheet1_options_str  += '<option value="Casa Loma">Casa Loma</option>';
            VI1Sheet1_options_str  += '<option value="Centennial Scarborough">Centennial Scarborough</option>';
            VI1Sheet1_options_str  += '<option value="Church-Yonge Corridor">Church-Yonge Corridor</option>';
            VI1Sheet1_options_str  += '<option value="Clairlea-Birchmount">Clairlea-Birchmount</option>';
            VI1Sheet1_options_str  += '<option value="Clanton Park">Clanton Park</option>';
            VI1Sheet1_options_str  += '<option value="Cliffcrest">Cliffcrest</option>';
            VI1Sheet1_options_str  += '<option value="Corso Italia-Davenport">Corso Italia-Davenport</option>';
            VI1Sheet1_options_str  += '<option value="Danforth">Danforth</option>';
            VI1Sheet1_options_str  += '<option value="Danforth-East York">Danforth-East York</option>';
            VI1Sheet1_options_str  += '<option value="Don Valley Village">Don Valley Village</option>';
            VI1Sheet1_options_str  += '<option value="Dorset Park">Dorset Park</option>';
            VI1Sheet1_options_str  += '<option value="Dovercourt-Wallace Emerson-Juncti">Dovercourt-Wallace Emerson-Juncti</option>';
            VI1Sheet1_options_str  += '<option value="Downsview-Roding-CFB">Downsview-Roding-CFB</option>';
            VI1Sheet1_options_str  += '<option value="Dufferin Grove">Dufferin Grove</option>';
            VI1Sheet1_options_str  += '<option value="East End-Danforth">East End-Danforth</option>';
            VI1Sheet1_options_str  += '<option value="Edenbridge-Humber Valley">Edenbridge-Humber Valley</option>';
            VI1Sheet1_options_str  += '<option value="Eglinton East">Eglinton East</option>';
            VI1Sheet1_options_str  += '<option value="Elms-Old Rexdale">Elms-Old Rexdale</option>';
            VI1Sheet1_options_str  += '<option value="Englemount-Lawrence">Englemount-Lawrence</option>';
            VI1Sheet1_options_str  += '<option value="Eringate-Centennial-West Deane">Eringate-Centennial-West Deane</option>';
            VI1Sheet1_options_str  += '<option value="Etobicoke West Mall">Etobicoke West Mall</option>';
            VI1Sheet1_options_str  += '<option value="Flemingdon Park">Flemingdon Park</option>';
            VI1Sheet1_options_str  += '<option value="Forest Hill North">Forest Hill North</option>';
            VI1Sheet1_options_str  += '<option value="Forest Hill South">Forest Hill South</option>';
            VI1Sheet1_options_str  += '<option value="Glenfield-Jane Heights">Glenfield-Jane Heights</option>';
            VI1Sheet1_options_str  += '<option value="Greenwood-Coxwell">Greenwood-Coxwell</option>';
            VI1Sheet1_options_str  += '<option value="Guildwood">Guildwood</option>';
            VI1Sheet1_options_str  += '<option value="Henry Farm">Henry Farm</option>';
            VI1Sheet1_options_str  += '<option value="High Park North">High Park North</option>';
            VI1Sheet1_options_str  += '<option value="High Park-Swansea">High Park-Swansea</option>';
            VI1Sheet1_options_str  += '<option value="Highland Creek">Highland Creek</option>';
            VI1Sheet1_options_str  += '<option value="Hillcrest Village">Hillcrest Village</option>';
            VI1Sheet1_options_str  += '<option value="Humber Heights-Westmount">Humber Heights-Westmount</option>';
            VI1Sheet1_options_str  += '<option value="Humber Summit">Humber Summit</option>';
            VI1Sheet1_options_str  += '<option value="Humbermede">Humbermede</option>';
            VI1Sheet1_options_str  += '<option value="Humewood-Cedarvale">Humewood-Cedarvale</option>';
            VI1Sheet1_options_str  += '<option value="Ionview">Ionview</option>';
            VI1Sheet1_options_str  += '<option value="Islington-City Centre West">Islington-City Centre West</option>';
            VI1Sheet1_options_str  += '<option value="Junction Area">Junction Area</option>';
            VI1Sheet1_options_str  += '<option value="Keelesdale-Eglinton West">Keelesdale-Eglinton West</option>';
            VI1Sheet1_options_str  += '<option value="Kennedy Park">Kennedy Park</option>';
            VI1Sheet1_options_str  += '<option value="Kensington-Chinatown">Kensington-Chinatown</option>';
            VI1Sheet1_options_str  += '<option value="Kingsview Village-The Westway">Kingsview Village-The Westway</option>';
            VI1Sheet1_options_str  += '<option value="Kingsway South">Kingsway South</option>';
            VI1Sheet1_options_str  += '<option value="L&apos;Amoreaux">L&apos;Amoreaux</option>';
            VI1Sheet1_options_str  += '<option value="Lambton Baby Point">Lambton Baby Point</option>';
            VI1Sheet1_options_str  += '<option value="Lansing-Westgate">Lansing-Westgate</option>';
            VI1Sheet1_options_str  += '<option value="Lawrence Park North">Lawrence Park North</option>';
            VI1Sheet1_options_str  += '<option value="Lawrence Park South">Lawrence Park South</option>';
            VI1Sheet1_options_str  += '<option value="Leaside-Bennington">Leaside-Bennington</option>';
            VI1Sheet1_options_str  += '<option value="Little Portugal">Little Portugal</option>';
            VI1Sheet1_options_str  += '<option value="Long Branch">Long Branch</option>';
            VI1Sheet1_options_str  += '<option value="Malvern">Malvern</option>';
            VI1Sheet1_options_str  += '<option value="Maple Leaf">Maple Leaf</option>';
            VI1Sheet1_options_str  += '<option value="Markland Wood">Markland Wood</option>';
            VI1Sheet1_options_str  += '<option value="Milliken">Milliken</option>';
            VI1Sheet1_options_str  += '<option value="Mimico">Mimico</option>';
            VI1Sheet1_options_str  += '<option value="Morningside">Morningside</option>';
            VI1Sheet1_options_str  += '<option value="Moss Park">Moss Park</option>';
            VI1Sheet1_options_str  += '<option value="Mount Dennis">Mount Dennis</option>';
            VI1Sheet1_options_str  += '<option value="Mount Olive-Silverstone-Jamestown">Mount Olive-Silverstone-Jamestown</option>';
            VI1Sheet1_options_str  += '<option value="Mount Pleasant East">Mount Pleasant East</option>';
            VI1Sheet1_options_str  += '<option value="Mount Pleasant West">Mount Pleasant West</option>';
            VI1Sheet1_options_str  += '<option value="New Toronto">New Toronto</option>';
            VI1Sheet1_options_str  += '<option value="Newtonbrook East">Newtonbrook East</option>';
            VI1Sheet1_options_str  += '<option value="Newtonbrook West">Newtonbrook West</option>';
            VI1Sheet1_options_str  += '<option value="Niagara">Niagara</option>';
            VI1Sheet1_options_str  += '<option value="North Riverdale">North Riverdale</option>';
            VI1Sheet1_options_str  += '<option value="North St.James Town">North St.James Town</option>';
            VI1Sheet1_options_str  += '<option value="O&apos;Connor-Parkview">O&apos;Connor-Parkview</option>';
            VI1Sheet1_options_str  += '<option value="Oakridge">Oakridge</option>';
            VI1Sheet1_options_str  += '<option value="Oakwood Village">Oakwood Village</option>';
            VI1Sheet1_options_str  += '<option value="Old East York">Old East York</option>';
            VI1Sheet1_options_str  += '<option value="Palmerston-Little Italy">Palmerston-Little Italy</option>';
            VI1Sheet1_options_str  += '<option value="Parkwoods-Donalda">Parkwoods-Donalda</option>';
            VI1Sheet1_options_str  += '<option value="Pelmo Park-Humberlea">Pelmo Park-Humberlea</option>';
            VI1Sheet1_options_str  += '<option value="Playter Estates-Danforth">Playter Estates-Danforth</option>';
            VI1Sheet1_options_str  += '<option value="Pleasant View">Pleasant View</option>';
            VI1Sheet1_options_str  += '<option value="Princess-Rosethorn">Princess-Rosethorn</option>';
            VI1Sheet1_options_str  += '<option value="Regent Park">Regent Park</option>';
            VI1Sheet1_options_str  += '<option value="Rexdale-Kipling">Rexdale-Kipling</option>';
            VI1Sheet1_options_str  += '<option value="Rockcliffe-Smythe">Rockcliffe-Smythe</option>';
            VI1Sheet1_options_str  += '<option value="Roncesvalles">Roncesvalles</option>';
            VI1Sheet1_options_str  += '<option value="Rosedale-Moore Park">Rosedale-Moore Park</option>';
            VI1Sheet1_options_str  += '<option value="Rouge">Rouge</option>';
            VI1Sheet1_options_str  += '<option value="Runnymede-Bloor West Village">Runnymede-Bloor West Village</option>';
            VI1Sheet1_options_str  += '<option value="Rustic">Rustic</option>';
            VI1Sheet1_options_str  += '<option value="Scarborough Village">Scarborough Village</option>';
            VI1Sheet1_options_str  += '<option value="South Parkdale">South Parkdale</option>';
            VI1Sheet1_options_str  += '<option value="South Riverdale">South Riverdale</option>';
            VI1Sheet1_options_str  += '<option value="St.Andrew-Windfields">St.Andrew-Windfields</option>';
            VI1Sheet1_options_str  += '<option value="Steeles">Steeles</option>';
            VI1Sheet1_options_str  += '<option value="Stonegate-Queensway">Stonegate-Queensway</option>';
            VI1Sheet1_options_str  += '<option value="Tam O&apos;Shanter-Sullivan">Tam O&apos;Shanter-Sullivan</option>';
            VI1Sheet1_options_str  += '<option value="Taylor-Massey">Taylor-Massey</option>';
            VI1Sheet1_options_str  += '<option value="The Beaches">The Beaches</option>';
            VI1Sheet1_options_str  += '<option value="Thistletown-Beaumond Heights">Thistletown-Beaumond Heights</option>';
            VI1Sheet1_options_str  += '<option value="Thorncliffe Park">Thorncliffe Park</option>';
            VI1Sheet1_options_str  += '<option value="Trinity-Bellwoods">Trinity-Bellwoods</option>';
            VI1Sheet1_options_str  += '<option value="University">University</option>';
            VI1Sheet1_options_str  += '<option value="Victoria Village">Victoria Village</option>';
            VI1Sheet1_options_str  += '<option value="Waterfront Communities-The Island">Waterfront Communities-The Island</option>';
            VI1Sheet1_options_str  += '<option value="West Hill">West Hill</option>';
            VI1Sheet1_options_str  += '<option value="West Humber-Clairville">West Humber-Clairville</option>';
            VI1Sheet1_options_str  += '<option value="Westminster-Branson">Westminster-Branson</option>';
            VI1Sheet1_options_str  += '<option value="Weston">Weston</option>';
            VI1Sheet1_options_str  += '<option value="Weston-Pellam Park">Weston-Pellam Park</option>';
            VI1Sheet1_options_str  += '<option value="Wexford/Maryvale">Wexford/Maryvale</option>';
            VI1Sheet1_options_str  += '<option value="Willowdale East">Willowdale East</option>';
            VI1Sheet1_options_str  += '<option value="Willowdale West">Willowdale West</option>';
            VI1Sheet1_options_str  += '<option value="Willowridge-Martingrove-Richview">Willowridge-Martingrove-Richview</option>';
            VI1Sheet1_options_str  += '<option value="Woburn">Woburn</option>';
            VI1Sheet1_options_str  += '<option value="Woodbine Corridor">Woodbine Corridor</option>';
            VI1Sheet1_options_str  += '<option value="Woodbine-Lumsden">Woodbine-Lumsden</option>';
            VI1Sheet1_options_str  += '<option value="Wychwood">Wychwood</option>';
            VI1Sheet1_options_str  += '<option value="Yonge-Eglinton">Yonge-Eglinton</option>';
            VI1Sheet1_options_str  += '<option value="Yonge-St.Clair">Yonge-St.Clair</option>';
            VI1Sheet1_options_str  += '<option value="York University Heights">York University Heights</option>';
            VI1Sheet1_options_str  += '<option value="Yorkdale-Glen Park">Yorkdale-Glen Park</option>';
            sel_VI1Sheet1.innerHTML = VI1Sheet1_options_str;
            div_VI1Sheet1.appendChild(sel_VI1Sheet1);
            var lab_VI1Sheet1 = document.createElement('div');
            lab_VI1Sheet1.innerHTML = 'VI1 Sheet1';
            lab_VI1Sheet1.className = 'filterlabel';
            div_VI1Sheet1.appendChild(lab_VI1Sheet1);
            var reset_VI1Sheet1 = document.createElement('div');
            reset_VI1Sheet1.innerHTML = 'clear filter';
            reset_VI1Sheet1.className = 'filterlabel';
            reset_VI1Sheet1.onclick = function() {
                var options = document.getElementById("sel_VI1Sheet1").options;
                for (var i=0; i < options.length; i++) {
                    options[i].selected = false;
                }
                filterFunc();
            };
            div_VI1Sheet1.appendChild(reset_VI1Sheet1);
        </script>
    </body>
</html>
