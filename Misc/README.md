# Country Finder - CTF Write-up

**Challenge Name:** Country Finder
**Category:** Misc  

---

### Challenge Description

You are given some latitude and longitude coordinates after connecting to a remote server using the following command:

`nc chall.cbctf.xyz 33159`

Your task is to identify the country corresponding to each set of coordinates and submit the sorted country names. 


The game has a random number of rounds and you must repeat the process for each round.

After connecting to the server, you will receive a list of latitude and longitude pairs, similar to the following:
```Round 1/21
Latitude: 40.1792, Longitude: 44.4991
Latitude: -4.6796, Longitude: 55.4919
Latitude: -21.1789, Longitude: -175.1982
Latitude: 31.9522, Longitude: 35.9304
Latitude: 41.3775, Longitude: 64.5853
Latitude: 60.472, Longitude: 8.4689
Latitude: 60.1282, Longitude: 18.6435
Latitude: 53.9045, Longitude: 27.559
Latitude: -18.1248, Longitude: 178.4501
Latitude: 4.3947, Longitude: 18.558
Latitude: -29.6099, Longitude: 28.2336
Latitude: -0.2299, Longitude: -78.5249
Latitude: 64.1355, Longitude: -21.8954
Latitude: 38.861, Longitude: 71.2761
Latitude: 42.5063, Longitude: 1.5211
Latitude: 49.6118, Longitude: 6.1319
Latitude: 48.669, Longitude: 19.699
Latitude: 42.4411, Longitude: 19.2636
Latitude: 11.8251, Longitude: 42.5903
Latitude: 10.6918, Longitude: -61.2225
Latitude: 11.8815, Longitude: -15.617
Latitude: 41.2044, Longitude: 74.7661

Enter sorted names seprated by commas: 
```


The number of rounds varies randomly (e.g., 21, 26, 29 rounds). Manually finding the corresponding countries using latitude and longitude for each round is time-consuming. Thus, I automated the process using Python.

### Solution

I wrote a Python script ( with help of ai) that quickly retrieves the country names based on the latitude and longitude pairs using the OpenCage Geocoder API. The script can handle multiple rounds of the game efficiently.

Here’s the Python code:

```python
from opencage.geocoder import OpenCageGeocode
import re

# Your OpenCage API key (sign up at https://opencagedata.com/)
api_key = 'YOUR_OPENCAGE_API_KEY'  # Replace with your OpenCage API key
geocoder = OpenCageGeocode(api_key)

# Function to get the country name from latitude and longitude
def get_country(latitude, longitude):
    result = geocoder.reverse_geocode(latitude, longitude)
    if result and len(result):
        return result[0]['components'].get('country', 'Unknown')
    return "Unknown"

# Example input string of latitude and longitude coordinates
input_string = """
Latitude: 40.1792, Longitude: 44.4991
Latitude: -4.6796, Longitude: 55.4919
Latitude: -21.1789, Longitude: -175.1982
Latitude: 31.9522, Longitude: 35.9304

"""

# Regular expression to extract latitude and longitude from the input string
pattern = r"Latitude:\s*([-+]?\d*\.?\d+),\s*Longitude:\s*([-+]?\d*\.?\d+)"
matches = re.findall(pattern, input_string)

# Convert matches to float and find corresponding country names
country_names = [get_country(float(lat), float(lon)) for lat, lon in matches]

# Print the country names as a comma-separated list
print(", ".join(country_names))
```
## How to Use the Script
Install the required Python module opencage:

 ```bash```
 `pip install opencage`

Sign up for an API key at OpenCage Geocoder.

Replace `YOUR_OPENCAGE_API_KEY` in the script with your actual API key.

Copy and paste the list of latitude and longitude coordinates you receive from the server into the `input_string` variable.

Run the script. Wait for a few seconds. It will output the country names which is the input of your round.

Copy the sorted list of country names and paste it back into the terminal where the challenge is running.

Repeat the process for each round until you complete the challenge and receive the flag.

After Finishing all round final text will be:
`   Congratulations! Here is your flag: EWU{7h1s_1s_WhY_Y0u_N33d_7o_L3aRn_sC1p71nG_5xxxxxx1}`
