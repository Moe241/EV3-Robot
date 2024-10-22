# EV3 Robot: Tennis Ball Collection and Goal Scoring

This project involves programming a LEGO Mindstorms EV3 robot to autonomously navigate a course, collect 10 tennis balls, and release them into a designated goal. The project uses a client-server architecture, where the EV3 robot is the client, and a camera mounted on a tripod, which oversees the entire course, acts as the server. The camera captures images of the entire course, including the robot, tennis balls, obstacles, and goals. The server processes these images using a machine learning model trained with RoboFlow to assist the robot in navigating the course and performing its tasks.

## Table of Contents

- [Project Overview](#project-overview)
- [Features](#features)
- [Technologies](#technologies)
- [Getting Started](#getting-started)
  - [Prerequisites](#prerequisites)
  - [Installation](#installation)
- [How it Works](#how-it-works)
  - [Hardware Setup](#hardware-setup)
  - [Software Setup](#software-setup)
  - [Client-Server Architecture](#client-server-architecture)
  - [Machine Learning](#machine-learning)
- [Project Structure](#project-structure)
- [Challenges and Solutions](#challenges-and-solutions)
- [Contributing](#contributing)
- [License](#license)
- [Contact](#contact)

## Project Overview

This project involves building and programming an EV3 robot to:
- Autonomously navigate a course.
- Collect 10 tennis balls scattered around the course.
- Avoid obstacles using real-time object detection.
- Release the tennis balls into a designated goal.

The robot uses a camera mounted on a tripod that captures the entire course, including the robot, balls, obstacles, and the goal. The camera is connected to a server that processes images and sends recognition results back to the robot. A machine learning model trained with RoboFlow handles object recognition.

## Features

- **Autonomous Navigation**: The EV3 robot moves within the course, guided by object recognition data from the server.
- **Client-Server Communication**: The robot (client) sends requests to the camera (server) to receive real-time image recognition data.
- **Image Recognition**: Recognizes tennis balls, obstacles, the robot, and goals via a machine learning model.
- **Object Interaction**: The robot is capable of picking up tennis balls and releasing them into the goal.
- **Dynamic Obstacle Avoidance**: The robot avoids obstacles based on recognition data and onboard sensors.
- **Real-Time Feedback**: The robot adjusts its behavior based on real-time data provided by the camera and server.

## Technologies

- **Python**: Core programming language for both client-side (robot) and server-side (image recognition) logic.
- **EV3dev**: Operating system for the LEGO EV3 brick.
- **RoboFlow**: For image annotation and model training.
- **REST API**: Facilitates communication between the robot (client) and the camera server.
- **OpenCV**: Initially used for image processing but replaced with RoboFlow for object detection.
- **LEGO Mindstorms EV3**: The hardware platform for the robot.
- **Flask**: Lightweight web framework used to implement the server.

## Getting Started

### Prerequisites

- **LEGO EV3 Mindstorms Kit**: Includes motors and sensors to enable movement and interaction.
- **Camera on a Tripod**: A camera positioned to capture the entire course, including the robot.
- **EV3dev OS**: Installed on the EV3 brick (guide: [ev3dev.org](https://www.ev3dev.org/)).
- **Python 3.x**: Installed on both the EV3 brick (client) and the server.
- **RoboFlow Account**: For image annotation and model training.
- **Flask**: To run the server on the camera system.

### Installation

1. **Clone the repository**:
   ```bash
   git clone https://github.com/yourusername/EV3-Tennis-Ball-Collector.git
   cd EV3-Tennis-Ball-Collector
   ```

2. **Set up the server (camera system)**:
   On the camera system, install dependencies and start the Flask server for image recognition:
   ```bash
   pip install flask roboflow opencv-python
   ```
   Start the server:
   ```bash
   python server.py
   ```

3. **Set up the client (EV3 robot)**:
   On the EV3 brick, install the necessary dependencies:
   ```bash
   pip install ev3dev2 requests
   ```
   Run the robot control program:
   ```bash
   python main.py
   ```

4. **Configure RoboFlow API**:
   - Sign up on [RoboFlow](https://roboflow.com/).
   - Annotate images of the tennis balls, obstacles, and goals.
   - Train a model using RoboFlow and get your API key.
   - Update the `roboflow_config.py` file with your RoboFlow API key.

## How it Works

### Hardware Setup

- **EV3 Robot**: Equipped with motors for movement, a grabbing mechanism for collecting tennis balls, and sensors (touch, ultrasonic) for navigation and obstacle detection.
- **Camera (Server)**: Mounted on a tripod, the camera captures the entire course, including the robot and the objects in its environment. This setup allows the robot to send images of the whole course to the server for processing.
- **Communication**: The EV3 robot communicates with the camera server via HTTP requests to obtain object recognition data.

### Software Setup

- **Client (EV3 Robot)**: The robot is responsible for controlling movement and interacting with the environment (collecting balls, avoiding obstacles, and releasing balls).
  - Sends HTTP requests to the server to process images and receive object recognition data.
  
- **Server (Camera)**: The camera server is built using Flask and integrates RoboFlow for image recognition.
  - Receives images from the EV3 robot.
  - Uses the RoboFlow API to process the images and return object information (tennis balls, obstacles, goal, and robot’s position).
  
### Client-Server Architecture

- **Client (EV3 Robot)**:
  - Captures images of the entire course, including itself, and sends the image to the server.
  - Example request:
    ```python
    import requests
    response = requests.post("http://<server-ip>:5000/recognize", files={"image": image_data})
    result = response.json()
    ```
  - Based on the server’s response (ball, obstacle, or goal), the robot adjusts its movements or actions (collects ball, avoids obstacle, moves to goal, etc.).

- **Server (Camera)**:
  - Mounted on a tripod to capture the entire course.
  - Processes images using the RoboFlow API and returns recognized objects.
  - Example of using RoboFlow for image recognition:
    ```python
    from roboflow import Roboflow
    rf = Roboflow(api_key="your_api_key")
    model = rf.workspace().project("your_project").version(1).model
    prediction = model.predict("path/to/image.jpg")
    ```

### Machine Learning

We used **RoboFlow** for image recognition tasks:
- **Image Annotation**: Captured images of the entire course, with tennis balls, obstacles, the goal, and the robot visible, and annotated them in RoboFlow.
- **Model Training**: The annotated images were used to train a custom object detection model on RoboFlow.
- **Integration**: The trained model is accessed via the RoboFlow API. The server processes the images sent by the EV3 robot, and returns recognized objects, which guide the robot’s actions.

## Project Structure

```
.
├── README.md                       # Project documentation
├── main.py                         # Main robot control logic (client)
├── server.py                       # Flask server for image recognition (server)
├── ball_collector.py                # Logic for ball collection
├── navigation.py                   # Logic for robot navigation
├── image_recognition.py            # RoboFlow image recognition logic
├── requirements.txt                # Python dependencies
└── config/
    └── roboflow_config.py          # RoboFlow API key and configuration
```

## Challenges and Solutions

- **Initial OpenCV Limitations**: OpenCV was initially used for image recognition, but it struggled with identifying objects under different lighting conditions. Switching to RoboFlow allowed us to train a more reliable custom model.
- **Latency in Client-Server Communication**: The system experienced delays due to the client-server interaction, especially when processing high-resolution images. To resolve this, we reduced image resolution and optimized the server response time to ensure real-time performance.
- **Full-Course View**: The camera’s placement on a tripod gave us a full view of the course, but we needed to ensure that the robot was always visible. This was accomplished by setting up the tripod at a height and angle that could capture the entire course without obstruction.

## Contributing

Contributions are welcome! To contribute:
1. Fork the repository.
2. Create a feature branch (`git checkout -b feature/YourFeature`).
3. Commit your changes (`git commit -m 'Add some feature'`).
4. Push to the branch (`git push origin feature/YourFeature`).
5. Open a Pull Request.
