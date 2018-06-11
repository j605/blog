---
author: j605
categories:
- gEDA
comments: true
date: "2013-11-07T10:35:36Z"
link: https://j605.wordpress.com/2013/11/07/low-pass-filter-and-it-its-frequency-response/
slug: low-pass-filter-and-it-its-frequency-response
title: Low Pass Filter and Its frequency response
wordpress_id: 58
---

After hours and hours of getting a simple low pass filter to work, I couldn't help but share this. With a host of FOSS EDA tools in linux, I really couldn't resist start using it.  I got to credit Ashwith, for the wonderful [SPICE tutorials](http://ashwith.wordpress.com/category/tutorials/spice/) without which my life would have been more miserable.

I wouldn't like to repeat what Ashwith has already written, but I would like to show the schema and results that you can obtain with ngspice as well as matplotlib which are as good as any commercial software that you would use. This is the schema that I have used. It is pretty simple to construct and following Ashwith's recommendation to change the config file will prove helpful as it will automatically fill in the refdes values for you. This is the schema I have used, the default model of the opamp works fine.


![lpf-active1](http://j605.files.wordpress.com/2013/11/lpf-active1.png)


You can use ngspice to simulate the circuit. Even though ngspice has an inbuilt plotter, matplotlib is more visually appealing. Before running ngspice you have to get the netlist, to get the netlist do `gnetlist -v -g spice-sdb -o lpf-active1.net lpf-active1.sch`. The verbose option will give you valuable debugging information. Start ngspice with the netlist and run the circuit. After simulation, you can use the inbuilt plotter or export the data for later use. To export do, `wrdata file v(1,3)`. Now you can use this script from ['return 1;'](http://return1.net/blog/2012/Jan/22/plotting-ngspice-results-with-python) to generate visually appealing graphs like the one below.

![Frequency response characteristics of first order low pass filter](http://j605.files.wordpress.com/2013/11/lpf-active1-response.png) Frequency response characteristics of first order low pass filter.
