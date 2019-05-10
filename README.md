# GeoMap
Trying to add data to a geomap
from bokeh.core.properties import value
from bokeh.io import show, output_notebook
from bokeh.models import (Rect, Text, GeoJSONDataSource, DataRange1d, Range1d, ColumnDataSource, FactorRange, FixedTicker,
                          Legend, NumeralTickFormatter, LabelSet, LogColorMapper, HoverTool, LinearColorMapper,
                         GMapPlot, GMapOptions )
from bokeh.plotting import figure
from bokeh.transform import dodge
from bokeh.palettes import Pastel2, viridis, inferno, magma, Paired, Spectral
from bokeh.transform import factor_cmap, linear_cmap
from collections import Counter
from bokeh.layouts import gridplot
from bokeh.io import export_png
from bokeh.models.glyphs import MultiLine, Line
import geopandas as gpd

import itertools
import pandas as pd
import copy
import matplotlib.pyplot as plt
import numpy as np
 
output_notebook()

import itertools
import pandas as pd
import copy
import matplotlib.pyplot as plt
import numpy as np
 
output_notebook()

df_map2 = pd.read_csv('countries_geom2.csv', delimiter='\t', index_col=0)
world = gpd.read_file(gpd.datasets.get_path('naturalearth_lowres'))

world = gpd.read_file(gpd.datasets.get_path('naturalearth_lowres'))
europe = (world.loc[world['continent'] == 'Europe'])
europe.head()

geo1_source = GeoJSONDataSource(geojson=df_map2.to_json())
geo_source = GeoJSONDataSource(geojson=europe.to_json())

palette = ['#b9ef96', '#9ae968', '#7be23a', '#6cdf23', '#64dd17']
color_mapper = LinearColorMapper(palette=palette)

TOOLS = 'pan, wheel_zoom, box_zoom, reset, hover, save'
p = figure(plot_height=600, width=500, title='Europe',tools=TOOLS,
            x_range=(-30,60), y_range=(30,85),
           x_axis_location=None, y_axis_location=None)
p.patches('xs', 'ys', fill_alpha=0.7, fill_color='green', line_color='black', line_width=0.5, source=geo1_source)
hover = p.select_one(HoverTool)
hover.point_policy = 'follow_mouse'
hover.tooltips = [('Countries','@geo')]


show(p)


# with geocode from googlemaps




def Geocode(query):
    # do geocoding
    query = str(query)
    try:
        geocode_result = gm.geocode(query)[0]       
        latitude = geocode_result['geometry']['location']['lat']
        longitude = geocode_result['geometry']['location']['lng']
        return latitude,longitude
    except IndexError:
        return 0
        
def GeocodeLatLng(data):
    lat=[]                            # initialize latitude list
    lng=[]                            # initialize longitude list
    [maxRow, maxCol]=data.shape
                                      
    start = data.index[0]             # start from the first data
    end = data.index[maxRow-1]        # end at maximum number of row
    for i in range(start,end+1,1):    # iterate all rows in the data
        isSuccess=True                # initial Boolean flag
        query = data.index[i] # try set up query 
        result=Geocode(query)
        if result==0:         
            result=Geocode(query)
            if result==0:     # if still not successful,
                                 # try set up another query 
                result=Geocode(query)
                if Geocode(query)==0: # if still not successful,
                    isSuccess=False                                           # mark as unsuccessful
                    print(i, 'is failed')
                else:
                    print(i, result)
            else:
                print(i, result)
        else:
            print(i, result)
        if isSuccess==True:           # if geocoding is successful,
            # store the results
            lat.append(result[0])     # latitude
            lng.append(result[1])     # longitude
    return lat,lng

# call the geocoding function
[lat,lng]=GeocodeLatLng(df_c)

# we put the list of latitude,longitude into pandas data frame
df = pd.DataFrame(
    {'latitude': lat,
     'longitude': lng
    })
    

