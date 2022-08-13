# purple_air_data_logger
 A logger that will query purple air sensor(s) for data. That data will then be ingested into a TimeScaleDB PostGreSQL database.

## Usage PurpleAirPSQLDataLogger.py 

```bash
usage: PurpleAirPSQLDataLogger.py [-h] -db_usr DB_USR [-db_host DB_HOST] -db DB [-db_port DB_PORT] [-db_pwd DB_PWD] -paa_read_key PAA_READ_KEY
                                  [-paa_sensor_index PAA_SENSOR_INDEX] [-paa_multiple_sensor_request_flag PAA_MULTIPLE_SENSOR_REQUEST_FLAG]
                                  [-paa_multiple_sensor_request_json_file PAA_MULTIPLE_SENSOR_REQUEST_JSON_FILE]

Collect data from PurpleAir sensors and insert into a database!

options:
  -h, --help            show this help message and exit
  -db_usr DB_USR        The PSQL database user
  -db_host DB_HOST      The PSQL database host
  -db DB                The PSQL database name
  -db_port DB_PORT      The PSQL database port number
  -db_pwd DB_PWD        The PSQL database password
  -paa_read_key PAA_READ_KEY
                        The PurpleAirAPI Read key
  -paa_sensor_index PAA_SENSOR_INDEX
                        The PurpleAirAPI sensor index for sending a single sensor request
  -paa_multiple_sensor_request_flag PAA_MULTIPLE_SENSOR_REQUEST_FLAG
                        This is a flag that by default is false. When set to true, we expect a json config file with parameters that will tell us how to
                        format our multiple sensor request.
  -paa_multiple_sensor_request_json_file PAA_MULTIPLE_SENSOR_REQUEST_JSON_FILE
                        If paa_multiple_sensor_request_flag is defined then this parameter is required. It shall be the path to a json file containing
                        the parameters to send a multiple sensor request.
```

### PAA_MULTIPLE_SENSOR_REQUEST_JSON_FILE Example

Out of the parameters listed below only "fields" is required. The others are all optional according to PurpleAirAPI (PAA) documentation. If a field is not being used, mark it 'null' without the single quotes.
```json
{
    "fields": "A string comma delimited list of fields to retrieve",
    "location_type": null,
    "read_keys": null,
    "show_only": null,
    "modified_since": null,
    "max_age": null,
    "nwlng" : null,
    "nwlat": null,
    "selng": null,
    "selat": null
}
```
> Note: Refer to the PurpleAirAPI (PAA) documentation for more information. https://api.purpleair.com/#api-sensors-get-sensors-data

> Taken From the PurpleAirAPI documentation:

    Field	Type	Description
    fields	String	
    The 'Fields' parameter specifies which 'sensor data fields' to include in the response. It is a comma separated list with one or more of the following:

    Station information and status fields:
    name, icon, model, hardware, location_type, private, latitude, longitude, altitude, position_rating, led_brightness, firmware_version, firmware_upgrade, rssi, uptime, pa_latency, memory, last_seen, last_modified, date_created, channel_state, channel_flags, channel_flags_manual, channel_flags_auto, confidence, confidence_manual, confidence_auto

    Environmental fields:
    humidity, humidity_a, humidity_b, temperature, temperature_a, temperature_b, pressure, pressure_a, pressure_b

    Miscellaneous fields:
    voc, voc_a, voc_b, ozone1, analog_input

    PM1.0 fields:
    pm1.0, pm1.0_a, pm1.0_b, pm1.0_atm, pm1.0_atm_a, pm1.0_atm_b, pm1.0_cf_1, pm1.0_cf_1_a, pm1.0_cf_1_b

    PM2.5 fields:
    pm2.5_alt, pm2.5_alt_a, pm2.5_alt_b, pm2.5, pm2.5_a, pm2.5_b, pm2.5_atm, pm2.5_atm_a, pm2.5_atm_b, pm2.5_cf_1, pm2.5_cf_1_a, pm2.5_cf_1_b

    PM2.5 pseudo (simple running) average fields:
    pm2.5_10minute, pm2.5_10minute_a, pm2.5_10minute_b, pm2.5_30minute, pm2.5_30minute_a, pm2.5_30minute_b, pm2.5_60minute, pm2.5_60minute_a, pm2.5_60minute_b, pm2.5_6hour, pm2.5_6hour_a, pm2.5_6hour_b, pm2.5_24hour, pm2.5_24hour_a, pm2.5_24hour_b, pm2.5_1week, pm2.5_1week_a, pm2.5_1week_b

    PM10.0 fields:
    pm10.0, pm10.0_a, pm10.0_b, pm10.0_atm, pm10.0_atm_a, pm10.0_atm_b, pm10.0_cf_1, pm10.0_cf_1_a, pm10.0_cf_1_b

    Visibility fields:
    scattering_coefficient, scattering_coefficient_a, scattering_coefficient_b, deciviews, deciviews_a, deciviews_b, visual_range, visual_range_a, visual_range_b

    Particle count fields:
    0.3_um_count, 0.3_um_count_a, 0.3_um_count_b, 0.5_um_count, 0.5_um_count_a, 0.5_um_count_b, 1.0_um_count, 1.0_um_count_a, 1.0_um_count_b, 2.5_um_count, 2.5_um_count_a, 2.5_um_count_b, 5.0_um_count, 5.0_um_count_a, 5.0_um_count_b, 10.0_um_count 10.0_um_count_a, 10.0_um_count_b

    ThingSpeak fields, used to retrieve data from api.thingspeak.com:
    primary_id_a, primary_key_a, secondary_id_a, secondary_key_a, primary_id_b, primary_key_b, secondary_id_b, secondary_key_b

    For field descriptions, please see the 'sensor data fields'. section.

    location_typeoptional	Number	
    The location_type of the sensors.
    Possible values are: 0 = Outside or 1 = Inside.

    read_keysoptional	String	
    A read_key is required for private devices. It is separate to the api_key and each sensor has its own read_key. Submit multiple keys by separating them with a comma (,) character for example: key-one,key-two,key-three.

    show_onlyoptional	String	
    A comma (,) separated list of sensor_index values. When provided, the results are limited only to the sensors included in this list.

    modified_sinceoptional	long	
    The modified_since parameter causes only sensors modified after the provided time stamp to be included in the results. Using the time_stamp value from a previous call (recommended) will limit results to those with new values since the last request. Using a value of 0 will match sensors modified at any time.

    max_ageoptional	Integer	
    Filter results to only include sensors modified or updated within the last number of seconds. Using a value of 0 will match sensors of any age.

    Default value: 604800

    nwlngoptional	Number	
    A north west longitude for the bounding box.

    Use a bounding box to limit the sensors returned to a specific geographic area. The bounding box is defined by two points, a north west latitude/longitude and a south east latitude/longitude.

    nwlatoptional	Number	
    A north west latitude for the bounding box.

    selngoptional	Number	
    A south east longitude for the bounding box.

    selatoptional	Number	
    A south east latitude for the bounding box.
