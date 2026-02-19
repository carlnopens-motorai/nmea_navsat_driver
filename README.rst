nmea_navsat_driver
===============

ROS driver to parse NMEA strings and publish standard ROS NavSat message types. Does not require the GPSD daemon to be running.

API
---

This package has no released Code API.

The ROS API documentation and other information can be found at http://ros.org/wiki/nmea_navsat_driver

MOTORAi Section
---
How it works
===============
UDP read: - Code in src/libnmea_navsat_driver/nodes/nmea_socket_driver.py creates a UDP socket, binds to the configured IP/port, loops calling recvfrom(), decodes bytes as ASCII, splits on newlines, and calls driver.add_sentence(...) for each NMEA sentence.

Parse → Fix publish: - Ros2NMEADriver.add_sentence() in src/libnmea_navsat_driver/driver.py checks the NMEA checksum, parses the sentence with the NMEA parser, builds a sensor_msgs/NavSatFix (NavSatFix) message (sets header stamp/frame_id, latitude/longitude/altitude, covariance), and publishes it on the fix topic via self.fix_pub.publish(...).

Message selection/details: - GGA sentences (preferred unless useRMC is set) produce full fixes with altitude and covariance derived from HDOP/EPE/GST when available; RMC can publish fixes (no altitude, unknown covariance) only if useRMC is enabled. TimeReference messages are published separately when GNSS time isn't used.

Topic & type: - Published topic: fix; message type: sensor_msgs/NavSatFix.