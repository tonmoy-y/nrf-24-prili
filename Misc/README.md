# Country Finder - CTF Write-up

**Challenge Name:** Country Finder <br>
**Category:** Misc  

---

### Challenge Description

You are given some latitude and longitude coordinates after connecting to a remote server using the following command:

`nc chall.cbctf.xyz 33159`

Your task is to identify the country corresponding to each set of coordinates and submit the sorted country names. 

### Solution

After connecting to the server, you will receive a list of latitude and longitude pairs, similar to the following:
```
Round 1/21
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

The game consists of a varying number of random rounds. To explore whether it's possible to bypass or reduce the number of rounds, I experimented by submitting incorrect inputs. Initially, upon connecting, I encountered 29 rounds. After submitting a wrong input and reconnecting, the number of rounds dropped to 26, then 23, 22, back to 29, and eventually 21 rounds. When I saw 21 rounds, I decided it was time to solve the challenge.

It takes too much time to find countries from latitude and longitude coordinates one by one
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

# change input string after getting latitude and longitude from the 
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
- Install the required Python module opencage <br>
Command : 
`pip install opencage`
- Sign up for an API key at <a href="https://opencagedata.com/">OpenCage Geocoder</a>.
- Replace `YOUR_OPENCAGE_API_KEY` in the script with your actual API key.
- Copy and paste the list of latitude and longitude coordinates you receive from the server into the `input_string` variable. <br> Example:
```python
input_string = """
Latitude: -21.1789, Longitude: -175.1982
Latitude: 31.9522, Longitude: 35.9304
Latitude: 41.3775, Longitude: 64.5853
Latitude: 60.472, Longitude: 8.4689
Latitude: 60.1282, Longitude: 18.6435
Latitude: 53.9045, Longitude: 27.559
"""
```
- Run the script. Wait for a few seconds. Output will be printed in the terminal which is the input of your round.
- Copy the sorted list of country names and paste it back into the terminal where the challenge is running.
- Repeat the process for each round until you complete the challenge and got the flag.

<b> N.B. Be careful when you copy all the  Latitude, Longitude of a round. Copy all latitudes, longitudes. 
And everything in the output got from the script should be copied. 
If any latitude, longitude when copying you have to start again from the beggining.  </b>

---

### After Finishing all round final text will be:
`Congratulations! Here is your flag: EWU{7h1s_1s_WhY_Y0u_N33d_7o_L3aRn_sC1p71nG_xxxxxxxx}`
