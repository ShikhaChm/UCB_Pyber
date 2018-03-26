# UCB_Pyber
## Pyber Ride Sharing Data Analysis
    #Dependencies
	    import matplotlib.pyplot as plt
	    import plotly.plotly as py
	    import csv
	    import pandas as pd
	    import numpy as np

	    bubbles_mpl = plt.figure()
			#Ride data
	    rdf = pd.read_csv('ride_data.csv', delimiter=',', encoding="utf-8-sig")
			#City data
	    cdf = pd.read_csv('city_data.csv', delimiter=',', encoding="utf-8-sig")
		
	    print ("==================================")
	    print ("   Analysis of Pyber Dataset","\n", "==================================")
	    #Data frame with city and rides data merged
	    crdf = pd.merge(rdf,cdf,on="city", how="left")
	   crdf.head()
		
## Analysis of rides in city data
		#Average Fare ($) Per City
		#Total Number of Rides Per City
		#Total Number of Drivers Per City
		#City Type (Urban, Suburban, Rural)

		analysis_df = crdf.groupby('city').agg({'fare': 'mean', 'ride_id':'nunique', 'driver_count': 'first', 'type':'first'}).sort_values('driver_count', ascending = False) 
		#analysis_df = crdf.groupby('city').agg({'fare': 'mean', 'ride_id':'nunique', 'driver_count': 'first', 'type':'first'}).sort_values('ride_id', ascending = False) 

		analysis_df.columns = [ 'AvgFare/City', 'TotalRides/City','TotalDrivers/City','City_type']
		analysis_df.head()
		
## Analysis using Bubble Charts
		#Separate data frames for City_type
		urban = analysis_df[analysis_df['City_type'] == 'Urban']
		rural = analysis_df[analysis_df['City_type'] == 'Rural']
		suburban = analysis_df[analysis_df['City_type'] == 'Suburban']

		#Color Dictionary 
		color_scheme = {'Gold':'#FFD700', 'Light Sky Blue':'#87CEFA', 'Light Coral':'#F08080'}
		city_color = {'Urban': color_scheme['Gold'], 'Suburban': color_scheme['Light Sky Blue'], 'Rural': color_scheme['Light Coral']}

		plt.suptitle('Pyber Ride Sharing Data (2016)')

## Scatter plots per each city type
		plt.scatter(urban['TotalRides/City'], urban['AvgFare/City'], s = urban['TotalDrivers/City']*10, color = city_color['Urban'], edgecolor = 'black', label = 'Urban', alpha = .75)
		plt.scatter(rural['TotalRides/City'], rural['AvgFare/City'], s = rural['TotalDrivers/City']*10, color = city_color['Rural'], edgecolor = 'black', label = 'Rural', alpha = .75)
		plt.scatter(suburban['TotalRides/City'], suburban['AvgFare/City'], s = suburban['TotalDrivers/City']*10, color = city_color['Suburban'], edgecolor = 'black', label = 'Suburban', alpha = .75)

		#Printing scatter plots
		plt.title('Note : Bubble size correlates to driver count per City')
		plt.xlabel('Total Number of Rides(Per City)')
		plt.ylabel('Average Fare $(Per City)')

		lgnd = plt.legend(frameon = True, edgecolor = 'black')
		lgnd.legendHandles[0]._sizes = [80]
		lgnd.legendHandles[1]._sizes = [80]
		lgnd.legendHandles[2]._sizes = [80]
		plt.show()	
		
## Analysis using Pie Charts
		#% of Total Fares by City Type (Pie Charts)
		faresByType = crdf.groupby('type')['type', 'fare', 'ride_id', 'driver_count']
		tot_fare  = faresByType.sum()['fare']
		#faresByType.columns = [ 'City_Type', 'Fare', 'Rides','Total Drivers']
		labels = tot_fare.index

		colors = [city_color[n] for n in labels]
		explode = [0 , 0, .3]
		plt.pie(tot_fare, startangle = 90, colors = colors, labels = labels, autopct = "%1.1f%%", 
						shadow = True, wedgeprops = {'linewidth': .5, 'edgecolor': 'black'})

		#Pie chart display
		plt.title('% of Total Fares by City Type')
		plt.axis('equal')
		plt.show()

##  % of Total Rides by City Type
		tot_rides  = faresByType.count()['ride_id']
		labels = tot_rides.index

		colors = [city_color[n] for n in labels]
		explode = [0 , 0, .3]
		plt.pie(tot_rides, startangle = 90, colors = colors, labels = labels, autopct = "%1.1f%%", explode = explode, 
			shadow = True, wedgeprops = {'linewidth': .5, 'edgecolor': 'black'})

		#Pie chart display
		plt.title('% of Total Rides by City Type')
		plt.axis('equal')
		plt.show()

##  % of Total Drivers by City Type
		tot_drivers = cdf.groupby('type').sum()['driver_count']
		labels = tot_drivers.index

		colors = [city_color[n] for n in labels]
		explode = [0 , 0, .3]
		plt.pie(tot_drivers, startangle = 90, colors = colors, labels = labels, autopct = "%1.1f%%", 
			shadow = True, wedgeprops = {'linewidth': .5, 'edgecolor': 'black'})

		#Pie chart display
		plt.title('% of Total Drivers by City Type')
		plt.axis('equal')
		plt.show()


## "Observable trends based on the data
- Rural areas have least number of drivers while urban areas have the highest with respesctive higher fare rates
- It follows from the first observation that urban areas have highest num of drivers. South Bryanstad and Davidstown with the highest total drivers.
- Port Johnstad have the max number of rides/city however, it isn't the city with highest num of drivers.
