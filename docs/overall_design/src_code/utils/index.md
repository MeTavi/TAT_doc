---
layout: default
title: Utilities 
parent: Source Code
nav_order: 9
---
# Utilities


<style type="text/css">
.tg  {border-collapse:collapse;border-spacing:0;}
.tg td{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;
  overflow:hidden;padding:10px 5px;word-break:normal;}
.tg th{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;
  font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg .tg-0pky{border-color:inherit;text-align:left;vertical-align:top}
</style>
<table class="tg"><thead>
  <tr>
    <th class="tg-0pky">Attribute</th>
    <th class="tg-0pky">Data Type</th>
    <th class="tg-0pky">Description</th>
  </tr></thead>
<tbody>
  <tr>
    <td class="tg-0pky">stp_identifier</td>
    <td class="tg-0pky"><span style="color:#905;background-color:#ddd">`str`</span></td>
    <td class="tg-0pky">This is equivalent to the GTFS stop id.</td>
  </tr>
  <tr>
    <td class="tg-0pky">sloc_description_sepecified</td>
    <td class="tg-0pky"><span style="color:#905;background-color:#ddd">`str`</span></td>
    <td class="tg-0pky">Containing the stop name and the relative street name.</td>
  </tr>
  <tr>
    <td class="tg-0pky">loca_longitude</td>
    <td class="tg-0pky"><span style="color:#905;background-color:#ddd">`float64`</span></td>
    <td class="tg-0pky">longitude of the stop </td>
  </tr>
  <tr>
    <td class="tg-0pky">loca_latitude</td>
    <td class="tg-0pky"><span style="color:#905;background-color:#ddd">`float64`</span></td>
    <td class="tg-0pky">latitude of the stop</td>
  </tr>
  <tr>
    <td class="tg-0pky">loca_segment_ext_id</td>
    <td class="tg-0pky"><span style="color:#905;background-color:#ddd">`str`</span></td>
    <td class="tg-0pky">the unique link identifier corresponding to the <span style="color:#905;background-color:#ddd">`ID`</span> field on the HASTUS network</td>
  </tr>
  <tr>
    <td class="tg-0pky">loca_dist_inter1</td>
    <td class="tg-0pky"><span style="color:#905;background-color:#ddd">`float64`</span></td>
    <td class="tg-0pky">distance from the start of the link</td>
  </tr>
  <tr>
    <td class="tg-0pky" rowspan="2">loca_segment_side</td>
    <td class="tg-0pky"><span style="color:#905;background-color:#ddd">`float64`</span></td>
    <td class="tg-0pky">distance from the end of the link</td>
  </tr>
  <tr>
    <td class="tg-0pky"><span style="color:#905;background-color:#ddd">`int64`</span></td>
    <td class="tg-0pky">Indicating on which side of the road the stop is located, O indicating <span style="color:#905;background-color:#ddd">`To Destination`</span>.</td>
  </tr>
</tbody></table>