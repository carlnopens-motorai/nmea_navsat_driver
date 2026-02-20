==================
NMEA NAVSAT DRIVER
==================

Forked from https://github.com/ros-drivers/nmea_navsat_driver
This is the ROS2 branch.

How it works
------------

UDP read
~~~~~~~~
- Code in ``src/libnmea_navsat_driver/nodes/nmea_socket_driver.py`` creates a UDP socket, binds to the configured IP/port, loops calling ``recvfrom()``, decodes bytes as ASCII, splits on newlines, and calls ``driver.add_sentence(...)`` for each NMEA sentence.

Parse → Fix publish
~~~~~~~~~~~~~~~~~~~
- ``Ros2NMEADriver.add_sentence()`` in ``src/libnmea_navsat_driver/driver.py`` checks the NMEA checksum, parses the sentence with the NMEA parser, builds a ``sensor_msgs/NavSatFix`` (``NavSatFix``) message (sets header stamp/frame_id, latitude/longitude/altitude, covariance), and publishes it on the ``fix`` topic via ``self.fix_pub.publish(...)``.

Message selection/details
~~~~~~~~~~~~~~~~~~~~~~~~~
- ``GGA`` sentences (preferred unless the parameter ``useRMC`` is set) produce full fixes with altitude and covariance derived from HDOP/EPE/``GST`` when available; ``RMC`` can publish fixes (no altitude, unknown covariance) only if ``useRMC`` is enabled. ``TimeReference`` messages are published separately when GNSS time isn't used.

Topic & type
------------
- Published topic: ``fix``; message type: ``sensor_msgs/NavSatFix``.