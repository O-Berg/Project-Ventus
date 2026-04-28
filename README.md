**Abstract:**

  CanSat, as defined by the European Space Agency (ESA), is an educational and experimental
  satellite that is the size of a soda can, designed to provide hands-on learning opportunities for
  students to simulate real-world space missions and gain practical experience in aerospace,
  engineering and space technology. During the project, the CanSats are sent with rockets to an
  altitude of approximately 1000 metres, after which they start collecting data to be transmitted
  to the ground via radio in real-time. A CanSat project can be broken down into two major
  parts: the primary and secondary mission. The aim of the primary mission is the same for all
  teams and is to monitor air temperature and pressure and send these data back to the ground
  via telemetry at least once per second; the secondary mission is what makes a CanSat unique
  as the team decides upon the objective.
  
  This paper is as a consequence of a CanSat project whose secondary mission aims to
  determine a wind profile, which is of scientific as well as practical importance when e.g.
  rockets or weather balloons are to be launched; this involves collecting different types of data
  at high frequencies and performing sensor fusion algorithms to minimise sensor bias. More
  specifically, combining a GPS and barometers data with lower frequency as well as accuracy,
  but without drifting data, and an IMU (Inertial Measurement Unit) with high frequency, high
  accuracy but with drifting sensor data. The IMU consists of an accelerometer, a gyroscope,
  and a magnetometer. Through an extended Kalman filter (EKF), these data are fused to
  output accurate orientation data. By integrating linear acceleration, we can estimate the
  position of the probe at any given point in time. However, a small error in the sensor data will
  result in highly inaccurate position data over time when integrated, which is where the set of
  lower frequency sensors comes into play; their data is used in a Kalman filter (KF) to correct
  the IMU data in real-time. Our approach is different from most other implementations, as the
  IMU at hand provides pre-filtered data. We utilise this fact and propose a _Successive Kalman
  Filter_ (SKF), alternatively _S-Kalman Filter_, with several advantages, preserving
  performance. This way, we achieve an accurate model of the trajectory of the probe, with
  which we can calculate the force from winds at any given instance. By applying physical
  formulas, we can derive a wind profile, i.e. the speed of the wind, as well as its direction, as a
  function of altitude
