# RescueScope: Adaptive Multi-Target Vision for Emergency Response

RescueScope delivers a resilient perception stack purpose-built for emergency crews navigating chaotic flood, storm, or quake zones. It fuses rapid object discovery with durable trajectory management so that operators always know where survivors, responders, and obstacles are moving.

The pipeline ingests video from drones, elevated rigs, or mobile units, stabilises the feed, and hands detection candidates to a tracking core that balances speed with identity reliability. Real-time overlays and structured telemetry can be relayed back to command dashboards, providing an actionable map when visibility on the ground collapses.

<p align="center">
  <img src="https://github.com/yudhisteer/Real-time-Multi-Object-Tracking-for-Rescue-Operations/assets/59663734/e3e921a6-ea73-4168-880f-0bba82a7bf4b" width="60%" />
</p>

## Field Footage Snapshot
This capture shows the tracker maintaining IDs through intense rainfall and reflections. Bounding boxes are colour-coded per identity so coordinators can spot diverging paths in seconds.

<div style="text-align: center;">
  <video src="https://github.com/yudhisteer/Real-time-Multi-Object-Tracking-for-Rescue-Operations/assets/59663734/d480ba65-4825-46c3-a200-f7e3d9b00b9c" controls="controls" style="max-width: 730px;">
  </video>
</div>

## Key Capabilities
- Multiclass awareness for people and vehicles with prioritisation hooks for mission-specific objects.
- Seamless switch between fast SORT-style motion models and appearance-aware Deep SORT for dense scenes.
- Lightweight Hungarian association implementation tuned for low-latency edge deployment.
- Configurable alert rules: dwell time inside geofences, crowding thresholds, and loss-of-tracking escalation.
- Export-ready tracks that slot into GIS systems or timeline reports for incident reviews.

## System Blueprint
### 1. Acquisition & Normalisation
`Background_subtraction.py` denoises, equalises exposure, and highlights motion pockets while maintaining a fall-back static background estimate for sudden lighting shifts.

### 2. Proposal Generation
The detector injects bounding boxes per frame. It is detector-agnostic, so feeds from YOLO models, classical cascades, or embedded sensors can plug in without altering downstream code.

### 3. Tracking Core
`SORT.py` keeps the loop lean with a Kalman prediction step and IOU-based assignment so small processors can keep up with 30 FPS feeds. `Custom_DeepSORT.py` layers appearance embeddings on top of motion cues, giving the system persistent identities even during deep occlusions.

### 4. Global Association
`Hungarian.py` wraps a clean implementation of the linear sum assignment routine. Cost matrices can mix IOU, centre distance, and appearance similarity, letting teams tailor trade-offs between responsiveness and stability.

<p align="center">
  <img src="https://github.com/yudhisteer/Real-time-Ego-Tracking-A-Tactical-Solution-for-Rescue-Operations/assets/59663734/a4294f57-94ea-4d66-8863-3dcf4830b165" width="70%" />
</p>

### 5. Identity Maintenance
Recovered trajectories are smoothed, aged, and tagged with confidence scores. Ageing logic retires stale tracks swiftly while allowing short occlusions to resolve organically.

<p align="center">
  <img src="https://github.com/yudhisteer/Real-time-Ego-Tracking-A-Tactical-Solution-for-Rescue-Operations/assets/59663734/d40b0246-bbe9-4191-adbf-16554e7adf93" width="90%" />
</p>

## Change Detection Playground
The background subtraction sandbox visualises how adaptive median modelling copes with camera shake, rain streaks, and moving foliage before candidates head into the tracker.

<div style="text-align: center;">
  <video src="https://github.com/yudhisteer/Real-time-Ego-Tracking-A-Tactical-Solution-for-Rescue-Operations/assets/59663734/38a4e14a-6e03-4ead-adee-7a9a13bb01a5" controls="controls" style="max-width: 730px;">
  </video>
</div>

<p align="center">
  <img src="https://github.com/yudhisteer/Real-time-Multi-Object-Tracking-for-Rescue-Operations/assets/59663734/d88c15a4-3b38-4f30-b4ab-4249531a0fde" />
</p>

<p align="center">
  <img src="https://github.com/yudhisteer/Real-time-Multi-Object-Tracking-for-Rescue-Operations/assets/59663734/5e355475-9173-42ab-aa7a-d77aa683dc6c" />
</p>

<p align="center">
  <img src="https://github.com/yudhisteer/Real-time-Multi-Object-Tracking-for-Rescue-Operations/assets/59663734/35b8a583-063c-40c6-ac55-d93cc4d0c44c" />
</p>

<p align="center">
  <img src="https://github.com/yudhisteer/Real-time-Multi-Object-Tracking-for-Rescue-Operations/assets/59663734/ad07dbea-23fe-44d1-b5b2-f34d1b3bfd8e" />
</p>

<p align="center">
  <img src="https://github.com/yudhisteer/Real-time-Multi-Object-Tracking-for-Rescue-Operations/assets/59663734/f6ebd4d8-83fd-4ef6-b5c3-2dcfdfea0922" />
</p>

<p align="center">
  <img src="https://github.com/yudhisteer/Real-time-Multi-Object-Tracking-for-Rescue-Operations/assets/59663734/3caa0642-3e2d-46e7-beb1-88be576a858e" />
</p>

## Assignment Visuals
These diagrams walk through how the cost matrix evolves before the Hungarian solver snaps detections to legacy IDs. They help calibrate IOU thresholds and diagnose identity swaps.

<p align="center">
  <img src="https://github.com/yudhisteer/Multi-Object-Tracking-with-Deep-SORT/assets/59663734/00b43dbd-7929-4ec2-9023-09b6a4e47e45" width="70%" />
</p>

<p align="center">
  <img src="https://github.com/yudhisteer/Multi-Object-Tracking-with-Deep-SORT/assets/59663734/95b5f22f-13bc-48b1-9fe7-ac7db1090f85" width="50%" />
</p>

<p align="center">
  <img src="https://github.com/yudhisteer/Multi-Object-Tracking-with-Deep-SORT/assets/59663734/38d83258-89c1-424a-ad84-8ec151d62090" width="50%" />
</p>

## Running the Toolkit
1. Create a Python environment (3.9+ recommended) and install dependencies listed in `requirements.txt` or within your preferred detector package.
2. Provide video sources via file path, RTSP stream, or webcam ID. Multiple feeds can be queued through a dispatcher script.
3. Choose `SORT.py` for rapid prototyping or `Custom_DeepSORT.py` when appearance features are available.
4. Tune IOU and feature similarity thresholds to match the density of the operational scene.
5. Enable telemetry export to persist track histories for post-incident analysis.

## Evaluation Checklist
- Track continuity across 500+ frames without identity resets.
- Latency budget below 120 ms per frame on mid-tier GPUs and sub-250 ms on CPU-only deployments.
- False positive filtering through minimum hit streaks and unmatch age logic.
- Configurable metrics (MOTA, MOTP, IDF1) surfaced for quick regression testing after model updates.

## Operational Playbooks
- **Flood rescues:** follow vehicles carried by water and flag when occupants exit or signals disappear.
- **Storm response:** keep continuous visibility on power crews and overhead hazards while communicating safe approach vectors.
- **Mass gatherings:** count and monitor crowd flow to guide evacuation corridors when weather or structural threats emerge.

## Roadmap
- Expand to 3D localisation using stereo rigs mounted on UAVs.
- Integrate semantic segmentation for debris categorisation.
- Add plug-ins for thermal sensors to improve low-light performance.
- Deliver packaged dashboards that render tracks, heatmaps, and dwell summaries in the browser.

## Full Deep SORT Run
Below is a longer deployment clip highlighting how identity preservation survives overlapping pedestrians and vehicles while the environment shifts dramatically.

<div style="text-align: center;">
  <video src="https://github.com/yudhisteer/Real-time-Multi-Object-Tracking-for-Rescue-Operations/assets/59663734/d480ba65-4825-46c3-a200-f7e3d9b00b9c" controls="controls" style="max-width: 730px;">
  </video>
</div>
