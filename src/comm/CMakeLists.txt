ADD_SUBDIRECTORY(dds)
ADD_SUBDIRECTORY(lcm)
ADD_SUBDIRECTORY(messenger)

INCLUDE_DIRECTORIES(.
  ${GLIB2_MAIN_INCLUDE_DIR}
  ${GLIB2_INTERNAL_INCLUDE_DIR}
  ${GTHREAD2_MAIN_INCLUDE_DIR}
)

INCLUDE_DIRECTORIES(../external/nmea/include)
INCLUDE_DIRECTORIES(lcm)
IF(GPS_FOUND)
INCLUDE_DIRECTORIES(${GPS_INCLUDE_DIR})
ENDIF(GPS_FOUND)

PIXHAWK_EXECUTABLE(mavconn-ping mavconn-ping.cc)
PIXHAWK_LINK_LIBRARIES(mavconn-ping
  mavconn_lcm
  ${GLIB2_LIBRARY}
  ${GTHREAD2_LIBRARY}
)

PIXHAWK_EXECUTABLE(mavconn-bridge-serial mavconn-bridge-serial.cc)
PIXHAWK_LINK_LIBRARIES(mavconn-bridge-serial
  mavconn_lcm
  ${GLIB2_LIBRARY}
  ${GTHREAD2_LIBRARY}
  ${Boost_PROGRAM_OPTIONS_LIBRARY}
)

PIXHAWK_EXECUTABLE(mavconn-bridge-udp mavconn-bridge-udp.cc)
PIXHAWK_LINK_LIBRARIES(mavconn-bridge-udp
  mavconn_lcm
  ${GLIB2_LIBRARY}
  ${GTHREAD2_LIBRARY}
)

IF(GLIBMM2_FOUND)
IF(SIGC++_FOUND)
IF(JPEG_TURBO_FOUND)
IF(RTI_FOUND)
INCLUDE_DIRECTORIES(
  ${JPEG_TURBO_INCLUDE_DIR}
  ${GLIBMM2_MAIN_INCLUDE_DIR}
  ${GLIBMM2_INTERNAL_INCLUDE_DIR}
  ${SIGC++_INCLUDE_DIR}
  ${RTI_INCLUDE_DIR}
  ${RTI_INCLUDE_DIR}/ndds
)

IF(CMAKE_SYSTEM_NAME MATCHES "Linux")
  IF(CMAKE_SYSTEM_PROCESSOR MATCHES "i[3-6]|x86_64")
    IF(CMAKE_SIZEOF_VOID_P EQUAL 4)
      # Linux 32-bit
      SET(CMAKE_CXX_FLAGS "-DRTI_UNIX -DRTI_LINUX -DRTI_32BIT -m32")
    ELSE(CMAKE_SIZEOF_VOID_P EQUAL 4)
      # Linux 64-bit
      SET(CMAKE_CXX_FLAGS "-DRTI_UNIX -DRTI_LINUX -DRTI_64BIT -m64")
    ENDIF(CMAKE_SIZEOF_VOID_P EQUAL 4)
  ENDIF(CMAKE_SYSTEM_PROCESSOR MATCHES "i[3-6]|x86_64")
ENDIF(CMAKE_SYSTEM_NAME MATCHES "Linux")

PIXHAWK_EXECUTABLE(mavconn-bridge-dds mavconn-bridge-dds.cc PxZip.cc)
PIXHAWK_LINK_LIBRARIES(mavconn-bridge-dds
  mavconn_lcm
  mavconn_shm
  mavconn_dds
  dl
  nsl
  rt
  ${OPENCV_CORE_LIBRARY}
  ${OPENCV_HIGHGUI_LIBRARY}
  ${RTI_LIBRARIES}
  ${JPEG_TURBO_LIBRARY}
  ${GLIBMM2_LIBRARY}
  ${SIGC++_LIBRARY}
  ${ZLIB_LIBRARY}
)
ENDIF(RTI_FOUND)
ENDIF(JPEG_TURBO_FOUND)
ENDIF(SIGC++_FOUND)
ENDIF(GLIBMM2_FOUND)

PIXHAWK_EXECUTABLE_CONDITIONAL(mavconn-gpsd CONDITION GPS_FOUND FILES mavconn-gpsd.cc)
PIXHAWK_LINK_LIBRARIES(mavconn-gpsd
  mavconn_lcm
  ${GPS_LIBRARY}
  ${GPS_LIBRARY_OPTIMIZED}
  ${Boost_PROGRAM_OPTIONS_LIBRARY}
)

PIXHAWK_EXECUTABLE(mavconn-imagestreamer mavconn-imagestreamer.cc)
PIXHAWK_LINK_LIBRARIES(mavconn-imagestreamer
  mavconn_lcm
  ${Boost_PROGRAM_OPTIONS_LIBRARY}
  ${OPENCV_CORE_LIBRARY}
  ${OPENCV_IMGPROC_LIBRARY}
  ${OPENCV_HIGHGUI_LIBRARY}
  ${Boost_FILESYSTEM_LIBRARY}
  ${Boost_SYSTEM_LIBRARY}
  ${GLIB2_LIBRARY}
  ${GTHREAD2_LIBRARY}
)

IF(VICON_FOUND)
IF(CMAKE_SYSTEM_NAME MATCHES "Linux")
  IF(CMAKE_SYSTEM_PROCESSOR MATCHES "i[3-6]|x86_64")
    PIXHAWK_EXECUTABLE(mavconn-bridge-vicon PxTransform.cc mavconn-bridge-vicon.cc PxVicon.cc)
    PIXHAWK_LINK_LIBRARIES(mavconn-bridge-vicon
      mavconn_lcm
      ${OPENCV_CORE_LIBRARY}
      ${VICON_LIBRARY_OPTIMIZED}
      ${Boost_PROGRAM_OPTIONS_LIBRARY}
    )
  ENDIF(CMAKE_SYSTEM_PROCESSOR MATCHES "i[3-6]|x86_64")
ENDIF(CMAKE_SYSTEM_NAME MATCHES "Linux")
ENDIF(VICON_FOUND)
