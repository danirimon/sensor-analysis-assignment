# Stop Sign Annotation Protocol

## Setup & Tools

1. **Download VIA**
   - Get **VIA Video Annotator v3.0.13** from https://www.robots.ox.ac.uk/~vgg/software/via/
   - Unzip to a local folder and double-click 'via_video_annotator.html' to launch in your browser.

2. **Folder Structure**
   - Place all '.mp4' dashcam clips in a 'videos/' subfolder next to the VIA HTML file.

---

## Define Your Region Attributes
Before annotating, you must create the custom fields ("attributes") that VIA will use for each polygon.

1. **Open the Attributes Editor**
   - In the top toolbar, click the Attributes editor icon to enter edit mode.

2. **Add Seven Region Attributes**
   For each attribute below:

   1. Click the **+** (plus) button at the top of the Attributes list.
   2. In the new row:
      - **Name**: as specified below
      - **Anchor**: select **Spatial Region in a Video Frame**
      - **Input Type**: choose **Select** (or **Text** for 'ref_frame_ts')
      - **Options** (only for Select): click the text-box and enter the values exactly as specified below 
                                       
   | Attribute      | Input Type | Options                                |
   | -------------- | ---------- | -------------------------------------- |
   |  class         | Select     | stop_sign                              |
   |  ref_frame_ts  | Text       |                                        |
   |  behavior      | Select     | Full Stop, Rolling Stop, No Stop       |
   |  occlusion     | Select     | Yes, No                                |
   |  multi_signs   | Select     | Yes, No                                |
   |  blurred       | Select     | Yes, No                                |
   |  low_light     | Select     | Yes, No                                |

3. **Exit Edit Mode**
   - Click the Attributes editor icon again (or any blank space) to close the Attributes Editor and return to annotation mode.

---

## Load & Annotate Videos
1. In the top toolbar, click the **+** (plus) button, navigate to 'videos/', and select **all** '.mp4' files you will annotate.
2. All clips appear in VIA's file list. Select the first clip to start.

---

## Identify the Reference Frame
For each clip, in order:

1. Use VIA's playhead (or arrow keys) to find the first frame where:
   - The entire octagon is fully in view and front-facing (<= 15 degrees rotation).
   - "STOP" text is legible at normal zoom.
2. Pause on that frame - this is your **reference frame**.

## Draw the Stop-Sign Polygon

1. Click the **Polygon** tool.
2. Click each of the eight visible sign corners, then double-click to close.
3. Click the new polygon (handles turn white) to select it.

---

## Fill in Region Attributes
With the polygon selected, use the Attributes panel to set each field:

1. **class**: open the dropdown, choose **stop_sign**.
2. **ref_frame_ts**: click the text box, type the timestamp shown on VIA's timeline in 'HH:MM:SS.ss' (e.g. '00:00:13.67').
3. **behavior**: play a 4-second window centered on your reference frame (2 seconds before through 2 seconds after) using the play button, then choose one:
   - **Full Stop**: stops and pauses for at least 1 second
   - **Rolling Stop**: slows but never fully stops
   - **No Stop**: no appreciable slowdown
4. **occlusion**: choose **Yes** if more than 10% of the sign is blocked, else **No**.
5. **multi_signs**: choose **Yes** if more than one stop sign appears, else **No**.
6. **blurred**: choose **Yes** if the sign is visually blurred (focus or motion blur), else **No**.
7. **low_light**: choose **Yes** if the frame is under-exposed or too dark to clearly see the sign, else **No**.

---

## Repeat for All Clips

- After finishing one clip, select the next clip from the list and repeat steps above.
- You can save progress at any time by clicking the **Save** (floppy disk) icon in the toolbar.

---

## Export Annotations

1. Click **Save** to persist your work.
2. Click the **Import/Export** icon and choose **CSV**, then save the file.

### Example CSV rows:

```csv
clip_id,ref_frame_ts,behavior,occlusion,multi_signs,blurred,low_light
clip_001,00:00:12.34,Full Stop,No,Yes,No,Yes
clip_002,00:00:07.89,Rolling Stop,No,No,Yes,No
```

------------------------------------------------------------------------------------------------------------------------------------------

## 2. Assumptions & Trade-offs

**Video-only speed estimates**: all labels are assigned by visual judgment, so they approximate rather than precisely measure vehicle   speed, obstruction, or lighting conditions.
**Fixed 4 seconds window**: keeps every clip aligned, but really slow approaches can fall outside this frame.
**Single reference frame**:  annotate one frame per clip to ensure consistency and efficiency, at the cost of losing temporal context for framing and visibility changes.
**Grouped creep-forward**: any small roll after a full stop is still called "Full Stop" to avoid over-complicating things.

---

## 3. How This Helps Model Training

**Aligned context**: every example uses the same 4 s slice, so patterns are easier to learn.  
**Strong visual cues**: clear sign images teach both octagon shape and "STOP" lettering.  
**Quality filters**: 'blurred', 'occlusion', and 'low_light' flags let us drop or down-weight problematic examples.  
**Focused classes**: three behavior labels (Full Stop, Rolling Stop, No Stop) keep the task simple and reduce confusion.  
**Two-stage setup**: polygons train a sign detector first, then the stop-behavior classifier works on those detections.

