# 1. Distance Calculator

**Purpose**
This Python program calculates the air distance (great circle distance) between all pairs of places provided in a CSV file (places.csv) or a set of randomly generated places. It then prints the distances sorted in ascending order and provides the average distance and the closest pair to the average distance.

# 2. Setup
- Ensure that you have python downloaded
- Ensure the `argparse`, `csv`, `random`, `math`, and `itertools` modules are included if not in built in


### Dependancies
**argparse** for command-line argument parsing.
**csv** for reading the places from the CSV file.
**random** for generating random places.
**math** for mathematical functions.
**itertools** for combinations.

# 3. Usage

### Using predefined places from a CSV file

Ensure you have a file named `places.csv` in the same directory as the script with the following content:

    '''csv
    Name,Latitude,Longitude
    Alta,69.96887,23.27165
    Anchorage,61.21806,-149.90028
    Jakarta,-6.21462,106.84513
    London,51.50853,-0.12574
    Longyearbyen,78.22334,15.64689
    North pole,90,0
    Oslo,59.91273,10.74609
    South pole,-90,0
    Troll research station,-72.00194,2.53389
    Vardø,70.37048,31.11066

### Run the script without any arguements
if no arguements are given the program uses places.csv as input

    'python filename.py'
    'python Distance_Caculator.py' //for my script



### Generating Random Places
Specify the number of random places to generate with the -n argument:

    'python filename.py -n 3'
    'python Distance_Calculator.py -n 3' //(for this script)


**Example output**

        PS C:\Users\HP\Distance_Calculator> python Distance_Calculator.py -n 3
        Place0   Place1   9178.5 km
        Place1   Place2   13750.4 km
        Place0   Place2   14646.7 km
        
        Average distance: 12525.2 km. Closest pair: Place1 – Place2 13750.4 km.


### Code Explanation
The main script performs the following tasks
a)  *Parse in Arguements*
- Uses argparse to handle an optional interger arguement n

b) *Initialize Distance Calculator*
- if no arguement is provided, it read from places.csv
- if an arguement is specified, it generates specified number of random places

c) *Calculate Distance*

- uses harversine formula to calculate distance between all unique pairs of places

d) *Display Results*

- Prints the distances between all pairs of places, sorted in ascending order.
- Displays the average distance and the pair of places closest to this average.


### Script 
'''python

    #import necessary Modules for Distance Calculator class
    import argparse # to handle commandline arguements
    import csv      #to read and write csv files 
    import random   # to generate random number
    import math     # for Mathematical computation
    from itertools import combinations  #for unique combination of elements
    
    #Create DistanceCalculator class
    class DistanceCalculator:
        
        # Initialize a DistanceCalculator object.
        def __init__(self):
            self.R = 6371.0  # Earth radius in kilometers
    
        #Calculate the haversine distance between two points given their latitude and longitude coordinates.
        def haversine(self, lat1, lon1, lat2, lon2):
            # Convert latitude and longitude from degrees to radians
            lat1, lon1, lat2, lon2 = map(math.radians, [lat1, lon1, lat2, lon2])
            dlat = lat2 - lat1
            dlon = lon2 - lon1
            # Haversine formula to calculate the distance
            a = math.sin(dlat / 2)**2 + math.cos(lat1) * math.cos(lat2) * math.sin(dlon / 2)**2
            c = 2 * math.atan2(math.sqrt(a), math.sqrt(1 - a))
            # Calculate the distance using Earth's radius
            return self.R * c
        
        #  Read place data from a CSV file and return a list of tuples containing place information.
        def read_places_from_csv(self, filename):
            places = []
            with open(filename, 'r') as file:
                reader = csv.reader(file)
                next(reader)  # skip header
                for row in reader:
                    places.append((row[0], float(row[1]), float(row[2])))
            #Returns a list of tuples where each tuple contains (name, latitude, longitude) of a place
            return places
        
        #Generates n random places with unique names and random latitude and longitude coordinates.
        def generate_random_places(self, n):
            places = []
            for i in range(n):
                name = f"Place{i}"
                lat = random.uniform(-90, 90)
                lon = random.uniform(-180, 180)
                places.append((name, lat, lon))
            #returns the entire list of randomly generated places
            return places
    
    #Calculate the haversine distances between all pairs of places.
        def calculate_distances(self, places):
            distances = []
            pairs = list(combinations(places, 2))
            for p1, p2 in pairs:
                name1, lat1, lon1 = p1
                name2, lat2, lon2 = p2
                distance = self.haversine(lat1, lon1, lat2, lon2)
                distances.append((name1, name2, distance))
            #returns a list containing (name1, name2, distance) representing distances between pairs of places.
            return distances
        
        #Formats a distance value to a string with one decimal place followed by 'km'
        def format_distance(self, d):
            return "{:.1f} km".format(d)
        
    
    #The Main function calculates distances between places based on command-line arguments.
        def main():
            parser = argparse.ArgumentParser(description="Calculate distances between places.")
            parser.add_argument('-n', '--number', type=int, help='Number of random places to generate')
            args = parser.parse_args()
        
            calculator = DistanceCalculator()
        
            #Determine which set of places to use based on command-line argument
            if args.number is None:
                places = calculator.read_places_from_csv('places.csv')
            else:
                places = calculator.generate_random_places(args.number)
            
            distances = calculator.calculate_distances(places)
            distances.sort(key=lambda x: x[2])
        
            #Calculate total and average distances
            total_distance = sum(d[2] for d in distances)
            average_distance = total_distance / len(distances)
        
            # Find the pair of places closest to the average distance
            closest_pair = min(distances, key=lambda x: abs(x[2] - average_distance))
        
            # Determine the maximum length of place names for formatting output
            max_name_length = max(len(p[0]) for p in places)
        
            # Print distances between all pairs of places
            for p1, p2, d in distances:
                print(f"{p1:<{max_name_length+2}} {p2:<{max_name_length+2}} {calculator.format_distance(d)}")
            
            # Print average distance and closest pair of places
            print(f"\nAverage distance: {calculator.format_distance(average_distance)}. Closest pair: {closest_pair[0]} – {closest_pair[1]} {calculator.format_distance(closest_pair[2])}.")
        
        #Executes the main function to calculate distances between places based on command-line arguments
        if __name__ == '__main__':
            main()
        
        
    
'''
 



    