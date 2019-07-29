---
title: "Undervolting drastically reduces CPU power draw"
layout: single
author_profile: true
read_time: true
share: true
date: '2019-07-27 18:37:01 -0800'
categories: coding
---
<!-- BokehJS CDN -->
<script src="https://cdn.pydata.org/bokeh/release/bokeh-1.3.0.min.js"></script>
<script src="https://cdn.pydata.org/bokeh/release/bokeh-widgets-1.3.0.min.js"></script>
<script src="https://cdn.pydata.org/bokeh/release/bokeh-tables-1.3.0.min.js"></script>


<!-- Bokeh plot info -->
<script type="text/javascript">
  (function() {
    var fn = function() {
      Bokeh.safely(function() {
        (function(root) {
          function embed_document(root) {

          var docs_json = '{"38891f4b-ec7a-44fb-8d09-6d5091202895":{"roots":{"references":[{"attributes":{"callback":null,"data":{"index":[6],"power":{"__ndarray__":"MzMzMzOzQEA=","dtype":"float64","shape":[1]},"v_offset":[120]},"selected":{"id":"1062","type":"Selection"},"selection_policy":{"id":"1061","type":"UnionRenderers"}},"id":"1041","type":"ColumnDataSource"},{"attributes":{},"id":"1059","type":"UnionRenderers"},{"attributes":{"ticker":{"id":"1011","type":"BasicTicker"}},"id":"1014","type":"Grid"},{"attributes":{},"id":"1060","type":"Selection"},{"attributes":{"axis_label":"Power Consumption (W)","formatter":{"id":"1056","type":"BasicTickFormatter"},"ticker":{"id":"1016","type":"BasicTicker"}},"id":"1015","type":"LinearAxis"},{"attributes":{"fill_color":{"value":"firebrick"},"line_color":{"value":"firebrick"},"size":{"units":"screen","value":10},"x":{"field":"v_offset"},"y":{"field":"power"}},"id":"1043","type":"Circle"},{"attributes":{},"id":"1061","type":"UnionRenderers"},{"attributes":{},"id":"1016","type":"BasicTicker"},{"attributes":{"fill_alpha":{"value":0.1},"fill_color":{"value":"#1f77b4"},"line_alpha":{"value":0.1},"line_color":{"value":"#1f77b4"},"size":{"units":"screen","value":10},"x":{"field":"v_offset"},"y":{"field":"power"}},"id":"1044","type":"Circle"},{"attributes":{},"id":"1062","type":"Selection"},{"attributes":{"dimension":1,"ticker":{"id":"1016","type":"BasicTicker"}},"id":"1019","type":"Grid"},{"attributes":{},"id":"1063","type":"UnionRenderers"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1020","type":"PanTool"},{"id":"1021","type":"WheelZoomTool"},{"id":"1022","type":"BoxZoomTool"},{"id":"1023","type":"SaveTool"},{"id":"1024","type":"ResetTool"},{"id":"1025","type":"HelpTool"},{"id":"1026","type":"HoverTool"}]},"id":"1027","type":"Toolbar"},{"attributes":{"data_source":{"id":"1041","type":"ColumnDataSource"},"glyph":{"id":"1043","type":"Circle"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1044","type":"Circle"},"selection_glyph":null,"view":{"id":"1046","type":"CDSView"}},"id":"1045","type":"GlyphRenderer"},{"attributes":{},"id":"1064","type":"Selection"},{"attributes":{"callback":null,"data":{"index":[0,1,2,3,4,5],"power":{"__ndarray__":"mpmZmZnZREAzMzMzM3NEQGZmZmZm5kNAmpmZmZlZQkDNzMzMzAxCQAAAAAAAgEFA","dtype":"float64","shape":[6]},"v_offset":[0,20,40,60,80,100]},"selected":{"id":"1060","type":"Selection"},"selection_policy":{"id":"1059","type":"UnionRenderers"}},"id":"1035","type":"ColumnDataSource"},{"attributes":{"source":{"id":"1041","type":"ColumnDataSource"}},"id":"1046","type":"CDSView"},{"attributes":{"fill_color":{"value":"dodgerblue"},"line_color":{"value":"dodgerblue"},"size":{"units":"screen","value":10},"x":{"field":"v_offset"},"y":{"field":"power"}},"id":"1037","type":"Circle"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1065","type":"BoxAnnotation"},{"attributes":{},"id":"1020","type":"PanTool"},{"attributes":{"x":{"field":"v_offset"},"y":{"field":"power"}},"id":"1049","type":"Line"},{"attributes":{},"id":"1021","type":"WheelZoomTool"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"v_offset"},"y":{"field":"power"}},"id":"1050","type":"Line"},{"attributes":{"overlay":{"id":"1065","type":"BoxAnnotation"}},"id":"1022","type":"BoxZoomTool"},{"attributes":{},"id":"1023","type":"SaveTool"},{"attributes":{"data_source":{"id":"1047","type":"ColumnDataSource"},"glyph":{"id":"1049","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1050","type":"Line"},"selection_glyph":null,"view":{"id":"1052","type":"CDSView"}},"id":"1051","type":"GlyphRenderer"},{"attributes":{},"id":"1024","type":"ResetTool"},{"attributes":{},"id":"1056","type":"BasicTickFormatter"},{"attributes":{"callback":null,"data":{"index":[0,1,2,3,4,5,6],"power":{"__ndarray__":"mpmZmZnZREAzMzMzM3NEQGZmZmZm5kNAmpmZmZlZQkDNzMzMzAxCQAAAAAAAgEFAMzMzMzOzQEA=","dtype":"float64","shape":[7]},"v_offset":[0,20,40,60,80,100,120]},"selected":{"id":"1064","type":"Selection"},"selection_policy":{"id":"1063","type":"UnionRenderers"}},"id":"1047","type":"ColumnDataSource"},{"attributes":{"source":{"id":"1047","type":"ColumnDataSource"}},"id":"1052","type":"CDSView"},{"attributes":{},"id":"1025","type":"HelpTool"},{"attributes":{"callback":null},"id":"1002","type":"DataRange1d"},{"attributes":{"callback":null,"tooltips":[["Voltage Offset:","@v_offset mV"],["Power Consumption:","@power{0.0} W"]]},"id":"1026","type":"HoverTool"},{"attributes":{"callback":null},"id":"1004","type":"DataRange1d"},{"attributes":{},"id":"1006","type":"LinearScale"},{"attributes":{"below":[{"id":"1010","type":"LinearAxis"}],"center":[{"id":"1014","type":"Grid"},{"id":"1019","type":"Grid"}],"left":[{"id":"1015","type":"LinearAxis"}],"plot_height":400,"plot_width":400,"renderers":[{"id":"1039","type":"GlyphRenderer"},{"id":"1045","type":"GlyphRenderer"},{"id":"1051","type":"GlyphRenderer"}],"title":{"id":"1053","type":"Title"},"toolbar":{"id":"1027","type":"Toolbar"},"x_range":{"id":"1002","type":"DataRange1d"},"x_scale":{"id":"1006","type":"LinearScale"},"y_range":{"id":"1004","type":"DataRange1d"},"y_scale":{"id":"1008","type":"LinearScale"}},"id":"1001","subtype":"Figure","type":"Plot"},{"attributes":{"text":""},"id":"1053","type":"Title"},{"attributes":{},"id":"1008","type":"LinearScale"},{"attributes":{"data_source":{"id":"1035","type":"ColumnDataSource"},"glyph":{"id":"1037","type":"Circle"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1038","type":"Circle"},"selection_glyph":null,"view":{"id":"1040","type":"CDSView"}},"id":"1039","type":"GlyphRenderer"},{"attributes":{"source":{"id":"1035","type":"ColumnDataSource"}},"id":"1040","type":"CDSView"},{"attributes":{"axis_label":"Voltage Reduction (mV)","formatter":{"id":"1058","type":"BasicTickFormatter"},"ticker":{"id":"1011","type":"BasicTicker"}},"id":"1010","type":"LinearAxis"},{"attributes":{},"id":"1058","type":"BasicTickFormatter"},{"attributes":{"fill_alpha":{"value":0.1},"fill_color":{"value":"#1f77b4"},"line_alpha":{"value":0.1},"line_color":{"value":"#1f77b4"},"size":{"units":"screen","value":10},"x":{"field":"v_offset"},"y":{"field":"power"}},"id":"1038","type":"Circle"},{"attributes":{},"id":"1011","type":"BasicTicker"}],"root_ids":["1001"]},"title":"Bokeh Application","version":"1.3.0"}}';
          var render_items = [{"docid":"38891f4b-ec7a-44fb-8d09-6d5091202895","roots":{"1001":"72aee388-eadb-4658-a88f-d098acf727f7"}}];
          root.Bokeh.embed.embed_items(docs_json, render_items);

          }
          if (root.Bokeh !== undefined) {
            embed_document(root);
          } else {
            var attempts = 0;
            var timer = setInterval(function(root) {
              if (root.Bokeh !== undefined) {
                embed_document(root);
                clearInterval(timer);
              }
              attempts++;
              if (attempts > 100) {
                console.log("Bokeh: ERROR: Unable to run BokehJS code because BokehJS library is missing");
                clearInterval(timer);
              }
            }, 10, root)
          }
        })(window);
      });
    };
    if (document.readyState != "loading") fn();
    else document.addEventListener("DOMContentLoaded", fn);
  })();
</script>

I've spent a decent amount of time overclocking my computers in the past. I'm definitely no expert, but it's fun to fiddle with the CPU voltages and frequencies, trying to squeeze every last bit of performance out of the CPU. As the CPU frequency is raised beyond the manufacturers default setting, system performance, power draw, and heat produced will increase; at some point the system becomes unstable and will crash or refuse to boot. At this point, overclockers will often raise the CPU voltage, which tends to make the system more stable, at the cost of additional heat production. If your only concern is maximizing performance, you can fight the increased temperatures with a better CPU cooler. Underclocking, by contrast, is the pursuit of *lower* clocks to minimize power draw and thermal loading. At lower clock frequencies, it may be possible to reduce the CPU voltage, i.e. *undervolt*, way below the default setting without compromising the system stability, allowing for exceptionally low temperatures and power consumption.

Generally speaking the factory settings for CPU voltage and frequency tend to be conservative. If you're not interested in overclocking, this means you can often stand to reduce the CPU voltage a little bit without reducing the CPU clock - essentially reducing the CPU temperature and power consumption without sacrificing any performance at all. While the overclocking community is much bigger, there are a number of tools which make undervolting your CPU surprisingly easy.

I started off by installing both the [sysbench][sysbench] (community) benchmarking utility and [intel-undervolt][uvolt] (AUR), which allows you to change the CPU voltage in real time without going into the BIOS, making the process extremely fast. Start out by opening up a new terminal and entering `# intel-undervolt measure`, to show a persistent display of the current CPU temperatures, frequencies, and power consumption. The `# intel-undervolt read` command is also useful here, and will display the current *offset* in the various CPU voltages. By default, it should show something like this:

```
CPU (0): 0.00 mV
GPU (1): 0.00 mV
CPU Cache (2): 0.00 mV
System Agent (3): 0.00 mV
Analog I/O (4): 0.00 mV
```

To undervolt the CPU, edit `/etc/intel-undervolt.conf`. By default there are a lot of lines commented out - these seem to be advanced settings which you might want to change if, for example,  you wanted to change the CPU throttling temperature threshold. I didn't mess with any of these. Instead, look for the following lines:

```
undervolt 0 'CPU' 0
undervolt 1 'GPU' 0
undervolt 2 'CPU Cache' 0
undervolt 3 'System Agent' 0
undervolt 4 'Analog I/O' 0
```

The values on the right hand side modify the default CPU and integrated GPU (NOT your graphics card) voltages, and are given in units of mV. Start out by setting both the CPU and GPU voltages to -50 or so. In reality it doesn't really matter what you set these to as long as you make sure the values are negative (remember, we want to *undervolt*); if you undervolt too much the system won't be damaged, it'll just crash and you'll have to reboot. I didn't bother messing with the `CPU Cache`, `System Agent`, or `Analog I/O` voltages. Apply the new voltage setting by typing `# intel-undervolt apply`. The effect might not be immediately obvious unless a benchmark is running - I tested a number of different voltage settings with `sysbench cpu --threads=8 --time=300 run` running in the background; this benchmark pins my CPU usage to 100%, so the change in power consumption should be considered a best-case scenario here. On my i7-4770k, the reported power consumption decreased substantially as the voltage was drastically reduced:

<!-- This is where the plot actually appears -->
<div class="bk-root" id="72aee388-eadb-4658-a88f-d098acf727f7" data-root-id="1001"></div>

Unfortunately, the system wasn't stable below -100 mV, though I didn't do an exhaustive search. Finally, I made the changes permanent by `# systemctl enable intel-undervolt.service` and `# systemctl start intel-undervolt.service`. For a few minutes work, I was able to reduce the CPU power consumption by ~7 W, or ~16% (at 100% CPU load). Considering the fact that this power savings comes at zero cost to performance, I'm really pleased with how it turned out. In principle this should make the CPU less susceptible to thermal throttling, too.


[uvolt]: https://github.com/kitsunyan/intel-undervolt
[sysbench]: https://github.com/akopytov/sysbench