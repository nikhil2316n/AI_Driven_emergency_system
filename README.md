# AI_Driven_emergency_system
import geopy.distance
import folium
import requests
from geopy.geocoders import Nominatim

# Example function to calculate distance between two locations
def calculate_distance(start_coords, end_coords):
    return geopy.distance.geodesic(start_coords, end_coords).km

# Example healthcare facility data (latitude, longitude, name)
healthcare_facilities = [
    {"name": "Hospital A", "latitude": 34.052235, "longitude": -118.243683},
    {"name": "Hospital B", "latitude": 35.052235, "longitude": -119.243683},
    {"name": "Clinic C", "latitude": 36.052235, "longitude": -120.243683},
    # Add more healthcare facilities as needed
]

# Function to get nearest healthcare facility based on patient's location
def find_nearest_healthcare(patient_coords):
    nearest_facility = None
    shortest_distance = float('inf')
    
    for facility in healthcare_facilities:
        facility_coords = (facility['latitude'], facility['longitude'])
        distance = calculate_distance(patient_coords, facility_coords)
        
        if distance < shortest_distance:
            shortest_distance = distance
            nearest_facility = facility
    
    return nearest_facility, shortest_distance

# Function to create a map showing the patient's location and nearest healthcare facility
def create_map(patient_coords, nearest_facility):
    m = folium.Map(location=patient_coords, zoom_start=10)
    
    # Adding patient location
    folium.Marker(patient_coords, popup="Patient Location", icon=folium.Icon(color='blue')).add_to(m)
    
    # Adding nearest healthcare facility
    folium.Marker(
        (nearest_facility['latitude'], nearest_facility['longitude']),
        popup=f"{nearest_facility['name']}",
        icon=folium.Icon(color='green')
    ).add_to(m)
    
    # Saving map to HTML file
    m.save("patient_route.html")
    print("Map saved to patient_route.html")

# Example patient location (latitude, longitude)
patient_coords = (34.101235, -118.243200)

# Find nearest healthcare facility
nearest_facility, distance = find_nearest_healthcare(patient_coords)

# Print the nearest facility and distance
if nearest_facility:
    print(f"Nearest healthcare facility: {nearest_facility['name']}")
    print(f"Distance: {distance:.2f} km")

# Create a map showing the patient and nearest healthcare facility
create_map(patient_coords, nearest_facility)

# Example function to get real-time conditions (e.g., weather)
def get_weather_conditions(location):
    # You can replace this with an actual weather API, e.g., OpenWeatherMap, for real-time data
    api_url = f"http://api.weatherapi.com/v1/current.json?key=YOUR_API_KEY&q={location[0]},{location[1]}"
    response = requests.get(api_url)
    weather_data = response.json()
    return weather_data['current']['condition']['text'], weather_data['current']['temp_c']

# Check weather conditions near the patient's location
weather_condition, temperature = get_weather_conditions(patient_coords)
print(f"Current weather: {weather_condition}, Temperature: {temperature}°C")

# Additional logic would be required to prioritize facilities based on real-time conditions, traffic, etc.
