---
layout: page
title: Reactors (Testing)
permalink: /reactors_testing/
no_menu: true
---
<link rel="stylesheet" href="/static/vender/leaflet/leaflet.css" />
<style>
.total.line { 
  stroke: black;
  stroke-width: 2;
  /*stroke-dasharray: 1, 1; */
  fill: yellow;
}
    .reac.line { 
      stroke: black;
      stroke-width: 0.5;
      stroke-dasharray: 2, 1;
      fill: none;
    }
    .iaea.line { 
      stroke: none;
      stroke-width: 0.5;
      stroke-dasharray: 2, 1;
      fill: green;
    }
    .c_reac.line{ 
      stroke: none;
      fill: #999;
    }
    .geo_u.line{ 
      stroke: blue;
      stroke-width: 2;
      fill: none;
    }
    .geo_th.line{ 
      stroke: red;
      stroke-width: 2;
      fill: none;
    }
    #detector_icon{
      position: absolute;
      top: 0px;
      pointer-events: none;
    }
    #reactor_icon{
      position: absolute;
      top: 0px;
      pointer-events: none;
    }
    #map_container{
			height:500px;
    }

    .axis path,
    .axis line {
      fill: none;
      stroke: grey;
      stroke-width: 1;
      shape-rendering: crispEdges;
    }
</style>
<div class="col-md-7">
  <div id="map_container">
  </div>
</div>
<div class="col-md-5">
<div id="application">
</div>

  <ul class="nav nav-tabs">
    <li class="active"><a href="#detector" data-toggle="tab">Detector</a></li>
    <li><a href="#reactor" data-toggle="tab">Reactors</a></li>
    <li><a href="#geonu" data-toggle="tab">GeoNu</a></li>
    <li><a href="#output" data-toggle="tab">Output &amp; Stats</a></li>
  </ul>

  <div class="tab-content">
    <div class="tab-pane active" id="detector">
      <div class="panel panel-default">
        <div class="panel-heading">Spectrum</div>
        <div class="panel-body">
          <div id="graph"></div>
          R<sub>Total</sub>: <span id="tnu_output"></span> TNU<br>
          R<sub>E &lt; 3.275 MeV</sub>: <span id="tnu_output_geo"></span> TNU<br>
          R<sub>Closest</sub>: <span id="tnu_output_close"></span> TNU (<span id="close_percent"></span>% of total)<br>
          Distance to Closest Reactor: <span id="closest_dist"></span> km<br>
          Distance to User Reactor: <span id="user_dist"></span> km
          <div class="checkbox">
            <label>
              <input id="mass_invert" type="checkbox"> Invert Neutrino Mass Hierarchy
            </label>
          </div>
          <small>1 TNU = 1 event/10<sup>32</sup> free protons/year</small>
        </div>
      </div>

    </div>
    <div class="tab-pane" id="reactor">
      <div class="panel panel-default">
        <div class="panel-heading">Reactor Load Factors</div>
        <div class="panel-body">
          <select id="load_factor" class="form-control">
            <!-- value="index" -->
            <option value="3">Mean LF</option>
            <option value="4">2013 LF</option>
          </select>
        </div>
      </div>
      <div class="panel panel-default">
        <div class="panel-heading">Custom Reactor</div>
        <div class="panel-body">

          <form class='form-horizontal'>
            <div class="form-group">
              <label class='col-sm-2 control-label' for="react_power">Power</label>
              <div class="input-group col-sm-10">
                <input type="number" class="form-control" id="react_power" placeholder="0" value='0'>
                <div class="input-group-addon">MW</div>
              </div>
            </div>

            <div class="form-group">
              <div class="col-sm-offset-2 col-sm-10">
                <div class="checkbox">
                  <label>
                    <input id="user_reactor" type="checkbox"> Use Custom Reactor
                  </label>
                </div>
              </div>
            </div>

          </form>

          <div class="panel panel-default">
            <div class="panel-heading">Location</div>
            <div class="panel-body">
              <form class="form-horizontal">


                <div class="form-group">
                  <label for="react_lat" class="col-sm-2 control-label">Latitude</label>
                  <div class="col-sm-10">
                    <input type="number" class="form-control input-sm" id="react_lat" value="0">
                  </div>
                </div>

                <div class="form-group">
                  <label for="react_lon" class="col-sm-2 control-label">Longitude</label>
                  <div class="col-sm-10">
                    <input type="number" class="form-control input-sm" id="react_lon" value="-103.75">
                  </div>
                </div>

                <button id="place_reactor" type="button" class="btn btn-success" data-toggle="tooltip" data-placement="right" title="Allows clicking the map to place the reactor">Place Reactor</button>

              </form>
            </div>
          </div>
        </div>
      </div>
    </div>

    <div class="tab-pane" id="geonu">
      <div class="panel panel-default">
        <div class="panel-heading">Mantle</div>
        <div class="panel-body">
          <form class="form-horizontal">
            <div class="form-group">
              <label for="mantle_signal" class="col-sm-4 control-label">Mantle Signal</label>
              <div class="col-sm-8">
                <div class="input-group">
                  <input type="number" min="0" step="0.1" class="form-control input-sm" id="mantle_signal" value="8.2">
                  <div class="input-group-addon">TNU</div>
                </div>
              </div>
            </div>
            <div class="form-group">
              <label for="react_lat" class="col-sm-4 control-label">Th/U Ratio</label>
              <div class="col-sm-8">
                <input type="number" min="0" class="form-control input-sm" id="thu_ratio" value="2.7" step="0.1">
              </div>
            </div>
          </form>


        </div>
      </div>
      <div class="panel panel-default">
        <div class="panel-heading">Crust</div>
        <div class="panel-body">
          <div class="checkbox">
            <label>
              <input id="include_crust" type="checkbox" checked> Include Crust Signal
            </label>
          </div>
          We use a pre-computed model of the crust flux provided by W.F. McDonough and described in Y. Huang et al., "A reference Earth model for the heat producing elements and associated geoneutrino flux," Geochem., Geophys., Geosyst. 14, 2003 (2013).
        </div>
      </div>

    </div>

    <div class="tab-pane" id="output">
      <div>
        <div class="panel panel-default">
          <div class="panel-heading">Calculator</div>
          <div class="panel-body">
            <form class="form-horizontal">
              <div class="form-group">
                <label class="col-sm-2 control-label">Signal</label>
                <div class="col-sm-10">
                  <select id="signal_source" class="form-control">
                    <option value="0">All Reactors (geoneutrino background)</option>
                    <option value="1">Closest Core (geonu + other reactors background)</option>
                    <option value="2">User Reactor (geonu + other reactors background)</option>
                    <option value="3">Geoneutrino (reactor background)</option>
                  </select>
                </div>
              </div>
              <div class="form-group">
                <label class="col-sm-2 control-label">Solve For</label>
                <div class="col-sm-10">
                  <select id="solve_for" class="form-control">
                    <option value="0">Exposure Time</option>
                    <option value="1">Significance</option>
                  </select>
                </div>
              </div>
              <div class="form-group">
                <label for="min_e" class="col-sm-2 control-label">E<sub>min</sub></label>
                <div class="col-sm-10">
                  <input type="number" step="0.1" class="form-control"
                  id="min_e" value="0" max="10" min="0">
                </div>
              </div>
              <div class="form-group">
                <label for="max_e" class="col-sm-2 control-label">E<sub>max</sub></label>
                <div class="col-sm-10">
                  <input type="number" step="0.1" class="form-control"
                  id="max_e" value="10" max="10" min="0">
                </div>
              </div>
              <div class="form-group">
                <label for="time" class="col-sm-2 control-label">Time (years)</label>
                <div class="col-sm-10">
                  <input type="number" step="0.1" class="form-control"
                  id="time" value="10" max="10" min="0">
                </div>
              </div>
              <div class="form-group">
                <label for="sigma" class="col-sm-2 control-label">Sigma</label>
                <div class="col-sm-10">
                  <input type="number" step="0.1" class="form-control"
                  id="sigma" value="3" max="10" min="0">
                </div>
              </div>
            </form>
            <small>Sigma = Signal * sqrt(Time) / sqrt(Signal + 2 * Background)</small>
          </div>
        </div>
      </div>
      <div>
        The box below contains the antineutrino energy spectrum and its components at the selected location. 
        The data, which range from 0 to 10 MeV, are in units of TNU (#/10^32 free protons/year) per 10 keV. 
        Comma-seperated columns of data correspond to: total, sum of known IAEA reactor cores, closest core, user defined core (0 if not using a custom reactor), and U and Th geoneutrino background. 
        There are a total of 1000 rows of data under each column. 
        The first 180 data rows have value 0 due to the energy threshold of the electron antineutrino inverse beta decay interaction on a free proton. 
        For plotting or further analysis, simply copy and paste the contents of this box into a text file or spreadsheet program. 
        Please cite this website if using these data as: Barna, A.M. and Dye, S.T., "Web Application for Modeling Global Antineutrinos," arXiv:1510.05633 (2015).
        <textarea readonly id="osc_out" rows="8" style="width:100%;">
        </textarea>
      </div>

    </div>


  </div>

</div>

<script src="/static/js/spherical_power.js"></script>
<script src="/static/cache/huang.js"></script>
<script src="/static/cache/geo_nu_spectra.js"></script>
<script>
// Enable the tool tips for all elements with them
$(function () {
  $('[data-toggle="tooltip"]').tooltip()
})

// Some global "switches" which alter certain behavior
// Mode determines what happens when clicking or mousing over the map:
// "normal" - follows the mouse around for spectrum
// "place_reactor" - clicking the map places a reactor at that location
// mass_invert inverts the neutrnio mass hierarchy when true
var mode = "normal";
var mass_invert = false;
var power_type = 3;

</script>

<script>
// this section contains some helpers to convert image coordinates to lat lon and the reverse
function image_coordinates_to_lon_lat(x, y, width, height){
  var lat_deg = (90 - (180/height) * y);
  var lon_deg = (x * (360/width) -180);

  return [lon_deg, lat_deg];
}
function lon_lat_to_image_coordinates(lon, lat, width, height){
  var x = ((90 - lat)/180) * height;
  var y  = ((180 + lon)/360) * width;

  return [x, y];
}
</script>

<script>

/*
Initalize the array and constants, this is almost
identical to the fortran except for the syntax.
 */
var spectrum = new Array(1000);
var e1 = 0.8;
var e2 = 1.43;
var e3 = 3.2;
var baserate = 0.00808;

// are all uninitialized variables in fortran 0?
var fluxnorm = 0;

/*
   In javascript current best way of looping through an array
   is to make counter variable (by convention i), the second 
   part of the loop is a test, in this case, that the counter i
   is not larger than the array is long, the third and final part
   of the loop is the action to be performed on the counter
   on each itteration, in this case it is incrimented by on (i++)

   javascript array indexes start at 0 instead of 1. This has the
   effect of having all the indicie tests be minus one from the
   fortran.
 */
for (var i=0; i < spectrum.length; i++){
  spectrum[i] = 0;
  if ((i > 178) && (i < 949)){
    var enu = i/100;
    // In javascript, to do x^y you need to call Math.pow(x, y)
    // Lets store the two parts seperately, then call the e^n function
    var p1 = Math.pow((enu-e2), 2);
    var p2 = -Math.pow((enu+e1)/e3, 2);
    spectrum[i] = p1 * Math.exp(p2);
    if (i > 338){
      // the += operator works as follows: a += b is identical to a = a + b
      fluxnorm += spectrum[i];
    }
  }
}
var scale = baserate/fluxnorm;
for (var i=0; i < spectrum.length; i++){
  spectrum[i] = spectrum[i] * scale;
}
var dmsq21 = 7.50e-5;
var ds2t13 = 0.19e-5;
var s2t13 = 0.0218;
var ds2t13 = 0.0010;
var s2t12 = 0.304;
var ds2t12 = 0.013;

var c4t13 = (1 - s2t13) * (1 - s2t13);
var s22t12 = 4 * s2t12 * (1 - s2t12);

//added nuosc13
var s22t13 = 4 * s2t13 * (1-s2t13);
var c2t12 = 1 - s2t12;

function geo_nu(lat, lon){
  pee = c4t13*(1.-s22t12*0.5)+s2t13*s2t13;
  // These "add one" operations are due to differences in how python
  // and Javascipt treat their "round" operations.
  if (lat < 0){
    lat += 1;
  }
  if (lon < 0){
    lon += 1;
  }
  var lat = Math.round(lat); 
  var lon = Math.round(lon);
  if (document.getElementById("include_crust").checked){
    var include_crust = 1;
  } else {
    var include_crust = 0;
  }
  var crust_u = huang[lat][lon]["U"] * 13.2 * pee * include_crust;
  var crust_th = huang[lat][lon]["Th"] * 4.0 * pee * include_crust;
  var user_mantle_signal = parseFloat(document.getElementById("mantle_signal").value);
  var user_mantle_ratio = parseFloat(document.getElementById("thu_ratio").value);
  var mantle_u = user_mantle_signal/(1 + 0.065*user_mantle_ratio);
  var mantle_th = user_mantle_signal - mantle_u;
  var total_u = crust_u + mantle_u;
  var total_th = crust_th + mantle_th;
  var u_spectra = new Array(1000);
  var th_spectra = new Array(1000);
  for (var i=0; i < geo_nu_spectra.u238.length; i++){
    u_spectra[i] = geo_nu_spectra.u238[i] * total_u * 1000;
  }
  for (var i=0; i < geo_nu_spectra.th232.length; i++){
    th_spectra[i] = geo_nu_spectra.th232[i] * total_th * 1000;
  }
  return {
    "u_tnu": total_u,
    "th_tnu": total_th,
    "u_spec": u_spectra,
    "th_spec": th_spectra
  }
}


/*
   In javascript, the return value is explictly passed back.
   So here we would create the array and just give it back to the caller
   of the function for them to deal with (usually assigned to some var)
 */
function nuosc(dist, pwr, spectrum, inverted){
  var earth_rad_sq = 4.059e7;
  var flux = pwr * earth_rad_sq / (dist * dist);

  var oscspec = new Array(1000);

  //locks the distance to integer kilometers
  var dist = Math.round(dist);

  if (inverted){
    var pee = neutrnio_survival_probability(dist);
  } else {
    var pee = inverted_neutrino_survival_probability(dist);
  }

  for (var i=0; i < oscspec.length; i++){
    oscspec[i] = 0;
    if (i >= 179){
      oscspec[i] = pee[i] * flux * spectrum[i];
    }
  }
  return oscspec;
}

/*
   Everything after here is making the output print on the webpage

   Just ignore unless you want to see how the SVG sausage is made
 */
function tof11(elm){
  return (elm/1000).toFixed(11);
}

</script>
<script src="/static/js/d3.v3.min.js"></script>

<script>
d3.select(window).on('resize', resize); 

function resize() {
  // update width
  width = parseInt(d3.select('#graph').style('width'), 10);
  width = width - margin.left - margin.right;

  // reset x range
  x.range([0, width]);

  // do the actual resize...
  d3.select(svg.node().parentNode)
    .style('width', (width + margin.left + margin.right) + 'px');

  svg.selectAll('.x.label')
    .attr('x', width);

  svg.select(".x.axis")
    .call(xAxis);

  svg.selectAll('rect.background')
    .attr('width', width);
  le.attr("transform", "translate(" + (width - 40) + ",0)");

  update_map();
}

// Set the dimensions of the graph
var margin = {top: 30, right: 20, bottom: 40, left: 50},
    width = parseInt(d3.select('#graph').style('width'), 10) - margin.left - margin.right,
    height = 270 - margin.top - margin.bottom;

// Set the ranges
var x = d3.scale.linear().range([0, width]);
var y = d3.scale.linear().range([height, 0]);

// Define the axes
var xAxis = d3.svg.axis().scale(x)
  .orient("bottom").ticks(8).tickFormat(function(d) {return ((d/100)+1).toFixed(0)});

var yAxis = d3.svg.axis().scale(y)
  .orient("left").ticks(5).tickFormat(function(d) {return (d/1000)});

  // Define the line
var valueline = d3.svg.line()
  .x(function(d, i) { return x(i); })
  .y(function(d) { return y(d); });

  // Adds the svg canvas
  var svg = d3.select("#graph")
  .append("svg")
  .attr("width", width + margin.left + margin.right)
  .attr("height", height + margin.top + margin.bottom)
  .append("g")
  .attr("transform", 
      "translate(" + margin.left + "," + margin.top + ")");

svg.append("path")
.attr("class", "total line");

svg.append("path")
.attr("class", "iaea line");

svg.append("path")
.attr("class", "c_reac line");

svg.append("path")
.attr("class", "geo_u line");
svg.append("path")
.attr("class", "geo_th line");

svg.append("path")
.attr("class", "reac line");


// Add the X Axis
svg.append("g")
.attr("class", "x axis")
.attr("transform", "translate(0," + height + ")")
.call(xAxis);

// Add the Y Axis
svg.append("g")
.attr("class", "y axis")
.attr("id", "yaxis")
.call(yAxis);

// Add the axis labels
svg.append("text")
.attr("class", "y label")
.attr("text-anchor", "end")
.style("font-size", "15px")
.attr("y", -50)
.attr("dy", ".75em")
.attr("transform", "rotate(-90)")
.text("Rate (TNU/10 keV)");
svg.append("text")
.attr("class", "x label")
.attr("text-anchor", "end")
.style("font-size", "15px")
.attr("x", width)
.attr("y", height + 33)
.text("Antineutrino Energy (MeV)");

le = svg.append("g")
.attr("transform", "translate(" + (width - 0) + ",0)");


le.append("text")
.attr("text-anchor", "end")
.attr("x", "-2.1em")
.attr("y", "0.3em")
.text("Total");
le.append("text")
.attr("text-anchor", "end")
.attr("x", "-2.1em")
.attr("y", "1.5em")
.text("Closest Reactor");
le.append("text")
.attr("text-anchor", "end")
.attr("x", "-2.1em")
.attr("y", "2.5em")
.text("Reactors");
le.append("text")
.attr("text-anchor", "end")
.attr("x", "-2.1em")
.attr("y", "3.5em")
.text("Geoneutrinos");
le.append("text")
.attr("text-anchor", "end")
.attr("x", "-2.1em")
.attr("y", "4.5em")
.text("Uranium");
le.append("text")
.attr("text-anchor", "end")
.attr("x", "-2.1em")
.attr("y", "5.5em")
.text("Thorium");
le.append("text")
.attr("text-anchor", "end")
.attr("class", "reac")
.attr("x", "-2.1em")
.attr("y", "6.6em")
.style("display", "none")
.text("User Reactor");

le.append("line")
.attr("x1", "-1.9em")
.attr("x2", "0")
.attr("y1", "0")
.attr("y2", "0")
.attr("stroke-width", 2)
.style("stroke", "#000");
le.append("line")
.attr("x1", "-1.9em")
.attr("x2", "0")
.attr("y1", "5.2em")
.attr("y2", "5.2em")
.attr("stroke-width", 2)
.style("stroke", "red");
le.append("line")
.attr("x1", "-1.9em")
.attr("x2", "0")
.attr("y1", "4.2em")
.attr("y2", "4.2em")
.attr("stroke-width", 2)
.style("stroke", "blue");

le.append("rect")
.attr("width", "1.9em")
.attr("height", "1em")
.attr("x", "-1.9em")
.attr("y", "0.5em")
.style("fill", "#999");
le.append("rect")
.attr("width", "1.9em")
.attr("height", "1em")
.attr("x", "-1.9em")
.attr("y", "1.5em")
.style("fill", "green");
le.append("rect")
.attr("width", "1.9em")
.attr("height", "1em")
.attr("x", "-1.9em")
.attr("y", "2.5em")
.style("fill", "yellow");

le.append("line")
.attr("x1", "-1.9em")
.attr("x2", "0")
.attr("y1", "6.3em")
.attr("y2", "6.3em")
.attr("stroke-width", 2)
.style("stroke", "#000")
.style("stroke-dasharray", "2,1")
.attr("class", "reac")
.style("display", "none");

var earth_radius = 6371;
var result;

//document.getElementById("the_map").addEventListener("mousemove", function(e){
//function follow_mouse(e){
//  if (mode == "place_reactor"){
//    return;
//  }
//  var checkbox = document.getElementById("is_locked");
//  if (!checkbox.checked){
//    return;
//  }
//  document.getElementById("detector_preset").value = "none";
//  xy = e.latlng;
//  while (xy.lng > 180){
//    xy.lng = xy.lng - 360;
//  }
//  while (xy.lng < -180){
//    xy.lng = xy.lng + 360;
//  }
//
//  document.getElementById("cursor_lat").value = xy.lat.toFixed(2);
//  document.getElementById("cursor_lon").value = xy.lng.toFixed(2);
//  update_map();
//}
//
//map.on("mousemove", follow_mouse);
//map.on("dragstart", function(e){map.off("mousemove", follow_mouse)});
//map.on("dragend", function(e){map.on("mousemove", follow_mouse)});

//document.getElementById("the_map").addEventListener("click", function(e){
//  if (mode == "normal"){
//    var checkbox = document.getElementById("is_locked");
//    if (checkbox.checked){
//      checkbox.checked = false;
//    } else {
//      checkbox.checked = true;
//    }
//  }
//  if (mode == "place_reactor"){
//    xy = image_coordinates_to_lon_lat(e.layerX, e.layerY, this.width, this.height);
//    document.getElementById("react_lat").value = xy[1].toFixed(2);
//    document.getElementById("react_lon").value = xy[0].toFixed(2);
//    mode = "normal";
//    $("#place_reactor").addClass("btn-success");
//    $("#place_reactor").removeClass("btn-danger");
//    document.getElementById("place_reactor").textContent = "Place Reactor";
//    update_map();
//  }
//});

document.getElementById("place_reactor").addEventListener("click", function(e){
  if ($(this).hasClass("btn-success")){
    $(this).removeClass("btn-success");
    $(this).addClass("btn-danger");
    this.textContent = "Cancel Reactor Placement";
    mode = "place_reactor";
  } else {
    mode = "normal";
    $(this).addClass("btn-success");
    $(this).removeClass("btn-danger");
    this.textContent = "Place Reactor";
  }
});

document.getElementById("cursor_lat").addEventListener("input", function(e){
  update_map();
});
document.getElementById("cursor_lon").addEventListener("input", function(e){
  update_map();
});
document.getElementById("react_lat").addEventListener("input", function(e){
  update_map();
});
document.getElementById("react_lon").addEventListener("input", function(e){
  update_map();
});
document.getElementById("mantle_signal").addEventListener("input", function(e){
  update_map();
});
document.getElementById("thu_ratio").addEventListener("input", function(e){
  update_map();
});
document.getElementById("react_power").addEventListener("input", function(e){
  update_map();
});
document.getElementById("user_reactor").addEventListener("change", function(e){
  update_map();
});
document.getElementById("include_crust").addEventListener("change", function(e){
  update_map();
});
document.getElementById("mass_invert").addEventListener("change", function(e){
  mass_invert = this.checked;
  update_map();
});
document.getElementById("signal_source").addEventListener("change", function(e){
  signal_stats();
});
document.getElementById("solve_for").addEventListener("change", function(e){
  signal_stats();
});
document.getElementById("min_e").addEventListener("input", function(e){
  signal_stats();
});
document.getElementById("max_e").addEventListener("input", function(e){
  signal_stats();
});
document.getElementById("time").addEventListener("input", function(e){
  signal_stats();
});
document.getElementById("sigma").addEventListener("input", function(e){
  signal_stats();
});

function distance(p1, p2){
  var dx = p1.x - p2.x;
  var dy = p1.y - p2.y;
  var dz = p1.z - p2.z;

  var dx2 = Math.pow(dx, 2);
  var dy2 = Math.pow(dy, 2);
  var dz2 = Math.pow(dz, 2);

  return Math.sqrt(dx2 + dy2 + dz2);
}

function squish_array(two_d_array){
  var output = new Array(two_d_array[0].length);
  for (var i=0; i < output.length; i++){
    output[i] = 0;
  }

  for (var i=0; i < two_d_array.length; i++){
    for (var ii=0; ii < output.length; ii++){
      output[ii] += two_d_array[i][ii];
    }
  }
  return output;
}


function update_map(){
  //var map_height = document.getElementById("the_map").height;
  //var map_width = document.getElementById("the_map").width;

  var lon_deg = parseFloat(document.getElementById("cursor_lon").value);
  var lat_deg = parseFloat(document.getElementById("cursor_lat").value);
  //var detct_image_coords = lon_lat_to_image_coordinates(lon_deg, lat_deg, map_width, map_height);
  //document.getElementById("detector_icon").style.top = detct_image_coords[0] + "px";
  //document.getElementById("detector_icon").style.left = detct_image_coords[1] + 15 + "px";

  var reac_lon_deg = parseFloat(document.getElementById("react_lon").value);
  var reac_lat_deg = parseFloat(document.getElementById("react_lat").value);
  //var react_image_coords = lon_lat_to_image_coordinates(reac_lon_deg, reac_lat_deg, map_width, map_height);
  //document.getElementById("reactor_icon").style.top = react_image_coords[0] + "px";
  //document.getElementById("reactor_icon").style.left = react_image_coords[1] + 15 + "px";
  var reac_lat = reac_lat_deg * (Math.PI/180);
  var reac_lon = reac_lon_deg * (Math.PI/180);
  var user_input = document.getElementById("user_reactor").checked;
  if (user_input){
    //document.getElementById("reactor_icon").style.display = "block";
  } else {
    //document.getElementById("reactor_icon").style.display = "none";
  }

  reac_p = {
    x : earth_radius * Math.cos(reac_lat) * Math.cos(reac_lon),
    y : earth_radius * Math.cos(reac_lat) * Math.sin(reac_lon),
    z : earth_radius * Math.sin(reac_lat)
  };
  reac_power = parseFloat(document.getElementById("react_power").value);

  var react_spectrum = [];
  var geo_nu_spectra = geo_nu(lon_deg, lat_deg);

  lat = lat_deg  * (Math.PI/180);
  lon = lon_deg * (Math.PI/180);

  p1 = {
    x : earth_radius * Math.cos(lat) * Math.cos(lon),
    y : earth_radius * Math.cos(lat) * Math.sin(lon),
    z : earth_radius * Math.sin(lat)
  };

  // we want to find the smallest element, so start someplace big...
  var min_dist = 1e10;
  var min_spec;
  for (var i=0; i<react_data.length; i++){

    p2 = {
      x : react_data[i][0],
      y : react_data[i][1],
      z : react_data[i][2]
    };

    power = react_data[i][power_type];
    dist = distance(p1, p2);
    var spec = nuosc(dist, power, spectrum, mass_invert);

    react_spectrum.push(spec);
    if ((dist < min_dist) && (d3.sum(spec) > 0)){
      min_dist = dist;
      min_spec = spec;
    }
  }
  var user_dist = distance(p1, reac_p);
  if (user_input){
    user_react_spectrum = nuosc(user_dist, reac_power, spectrum, mass_invert);
    document.getElementById("user_dist").textContent = user_dist.toFixed(0);
    d3.selectAll(".reac").style("display", "");
    if (user_dist < min_dist){
      min_dist = user_dist;
      min_spec = user_react_spectrum;
    }
  } else {
    user_react_spectrum = nuosc(user_dist, 0, spectrum, mass_invert);
    document.getElementById("user_dist").textContent = "N/A";
    d3.selectAll(".reac").style("display", "none");
  }
  document.getElementById("closest_dist").textContent = min_dist.toFixed(0);


  closest = min_spec;
  user_spec = squish_array([user_react_spectrum]);
  iaea = squish_array([squish_array(react_spectrum), user_spec]);
  total = squish_array([squish_array(react_spectrum), user_spec, geo_nu_spectra.u_spec, geo_nu_spectra.th_spec]);

  text_out = Array(1001);
  text_out[0] = "total,iaea,close,user,geo_u,geo_th";
  for (var i=0; i< iaea.length; i++){
    text_out[i+1] = tof11(total[i]) + "," + tof11(iaea[i])+","+ tof11(closest[i]) + "," + tof11(user_spec[i]) + "," + tof11(geo_nu_spectra.u_spec[i]) + "," + tof11(geo_nu_spectra.th_spec[i]);
  }
  result = {
    "total":total,
    "iaea":iaea,
    "close":closest,
    "user":user_spec,
    "geo_u":geo_nu_spectra.u_spec,
    "geo_th":geo_nu_spectra.th_spec,
  };

  document.getElementById("osc_out").value = text_out.join('\n');
  var total_tnu = (d3.sum(total)/1000).toFixed(1)
    var close_tnu = (d3.sum(closest)/1000).toFixed(1)
    document.getElementById("tnu_output").textContent = total_tnu;
  document.getElementById("tnu_output_close").textContent = close_tnu;
  document.getElementById("close_percent").textContent = (close_tnu/total_tnu * 100).toFixed(0);
  document.getElementById("tnu_output_geo").textContent = (d3.sum(total.slice(0, 326))/1000).toFixed(1);

  function for_plot(arr){
    arr = arr.slice(100,899);
    arr.push(0);
    return arr;
  }

  x.domain([0, d3.max(total, function(d, i) { return i; })- 200]);
  y.domain([0, d3.max(total, function(d) { return d; })]);
  svg.select(".reac")
    .attr("d", valueline(for_plot(user_spec)));
  svg.select(".c_reac")
    .attr("d", valueline(for_plot(closest)));
  svg.select(".geo_u")
    .attr("d", valueline(geo_nu_spectra.u_spec.slice(100,900)));
  svg.select(".geo_th")
    .attr("d", valueline(geo_nu_spectra.th_spec.slice(100,900)));
  svg.select(".total")
    .attr("d", valueline(for_plot(total)));
  svg.select(".iaea")
    .attr("d", valueline(for_plot(iaea)));
  svg.select("#yaxis")
    .call(yAxis);
  svg.select(".x.axis")
    .call(xAxis);
  signal_stats();
}
update_map();

function signal_stats(){
  var min_i = parseInt(parseFloat(document.getElementById("min_e").value) * 100);
  var max_i = parseInt(parseFloat(document.getElementById("max_e").value) * 100);

  var solve_for = document.getElementById("solve_for").value;
  var signal_source = document.getElementById("signal_source").value;

  if (signal_source == "0"){
    var signal = d3.sum(result.iaea.slice(min_i, max_i))/1000 + d3.sum(result.user.slice(min_i, max_i))/1000;
    var background = d3.sum(result.geo_u.slice(min_i, max_i))/1000 + d3.sum(result.geo_th.slice(min_i, max_i))/1000;
  } else if (signal_source == "1"){
    var signal = d3.sum(result.close.slice(min_i, max_i))/1000;
    var background = d3.sum(result.total.slice(min_i, max_i))/1000  - d3.sum(result.close.slice(min_i, max_i))/1000;
  } else if (signal_source == "2"){
    var signal = d3.sum(result.user.slice(min_i, max_i))/1000;
    var background = d3.sum(result.total.slice(min_i, max_i))/1000 - d3.sum(result.user.slice(min_i, max_i))/1000;
  } else if (signal_source == "3"){
    var background = d3.sum(result.iaea.slice(min_i, max_i))/1000 + d3.sum(result.user.slice(min_i, max_i))/1000;
    var signal = d3.sum(result.geo_u.slice(min_i, max_i))/1000 + d3.sum(result.geo_th.slice(min_i, max_i))/1000;
  }

  if (solve_for == "0"){
    document.getElementById("sigma").removeAttribute("readonly");
    var sigma = parseFloat(document.getElementById("sigma").value);;
    document.getElementById("time").setAttribute("readonly", "true");
    document.getElementById("time").value = ((signal + 2 * background) * (sigma/signal) * (sigma/signal)).toFixed(3);
  } else if (solve_for == "1"){
    document.getElementById("sigma").setAttribute("readonly", "true");
    var time = parseFloat(document.getElementById("time").value);
    document.getElementById("time").removeAttribute("readonly");
    var sigma = signal * Math.sqrt(time)/Math.sqrt(signal + 2 * background);
    document.getElementById("sigma").value = sigma.toFixed(2);
  }
}
</script>
  <script>
document.getElementById("load_factor").addEventListener("change", function(e){
  power_type = parseInt(this.value);
  update_map();
});
  </script>
<script src="/static/js/build/reactors.js"></script>