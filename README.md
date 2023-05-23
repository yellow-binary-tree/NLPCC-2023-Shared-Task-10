# NLPCC-2023-Shared-Task-10
Learn to Watch TV: Multimodal Dialogue Understanding and Response Prediction

## Overview
The multimodal dialogue understanding and response prediction task can be divided into two phases: multimodal context understanding and response prediction. Specifically, the former includes dialogue session identification (i.e., determining whether the dialogue content has changed) and dialogue scene identification (i.e., determining whether the video context has changed). The ultimate goal is to predict a response that is coherent with the dialogue topic and relevant to the video scene.

- Track 1: Dialogue Scene Identification: Predict the boundaries of different dialogue scenes given a set of continuous dialogue utterances and a related video. The scene label can be 0 or 1, where 0 denotes the current turn is in the same scene as the previous turn (i.e. the videos show the same scene), and 1 denotes the current turn starts a new scene.

- Track 2: Dialogue Session Identification: Predict the boundaries of different dialogue sessions given a set of continuous dialogue utterances and a related video (which is identical to Track 1). The session label can be 0 or 1, where 0 denotes the current turn is in the same session as the previous turn (i.e. the current turn is talking about the same topic), and 1 denotes the current turn starts a new session.

- Track 3: Dialogue Response Retrieval: Retrieve a response based on scene and session predictions, while coherently catching up with the conversation.

- Track 4. Dialogue Response Generation: Generate a response based on scene and session predictions, while coherently catching up with the conversation.

## Data
### Statistics
|       | clips  | utterances | scenes | sessions | utter/clip | scene/clip | session/clip | en_word/clip | en_word/utter | ch_word/clip | ch_word/utter |
| ----- | ------ | ---------- | ------ | -------- | ---------- | ---------- | ------------ | ------------ | ------------- | ------------ | ------------- |
| train | 40,006 | 1,000,079  | 56,535 | 106,078  | 25         | 1.41       | 2.65         | 166.46       | 6.66          | 267.74       | 10.71         |
| valid | 1,955  | 50,032     | 3,202  | 6,331    | 25.6       | 1.64       | 3.24         | 174.49       | 6.82          | 283.7        | 11.09         |


### Download Links and Details
#### Train and Valid Sets
Download from [BaiduNetDisk](https://pan.baidu.com/s/1paNUmL_qgzYpVQMyeA4whA?pwd=gy5s).

- Text

Example Data:
```
{"The.Night.Shift_S02E02": [
    {"vid": "The.Night.Shift_S02E02_clip_000", "subs": [
        {"turn_id": 0, "en_text": "So, about jordan.", "ch_text": "关于乔丹的事", "start": 0.0, "end": 2.24, "scene": 1, "session": 1}, 
        {"turn_id": 1, "en_text": "Ah, don't worry.", "ch_text": "别担心", "start": 0.0, "end": 2.24, "scene": 0, "session": 0},
        {"turn_id": 2, "en_text": "We're just taking it one day at a time.", "ch_text": "我们只想慢慢来", "start": 2.24, "end": 3.49, "scene": 0, "session": 0}, 
        ...]
    },
    {"vid": "The.Night.Shift_S02E02_clip_001", "subs": [...]}, ...
], "The.Night.Shift_S02E04": ...
}
```
where keys `start, end` in each turn marks the start second and end second of each turn in the clip (but not necessary for the tasks),
and keys `en_text, ch_text, scene, session` in each turn correspond to the dialogue in English/Chinese, the scene label, and the session label (which are the targets of the tasks).

- Video
Each video consists of several clips, each clip consists of several turns, and each turn consists of several frames (sampled at 3 fps). The frames are laid out as follows:
```
// [name of TV show]_S[season]_E[episode]_clip_[clip id]_dia_[turn id]_[image id].jpg
├── Friends_S01E01      # video name
    └── clip_000        # clip name
        └── Friends_S01E01_clip_000_dia_00_01.jpg     # the first frame of the first turn
        └── Friends_S01E01_clip_000_dia_00_02.jpg
        └── ...
        └── Friends_S01E01_clip_000_dia_01_01.jpg     # the first frame of the second turn 
        └── Friends_S01E01_clip_000_dia_01_02.jpg
        └── ...
    └── clip_001
        └── ...
    └── ...
├── ...
```

#### Test Set
We updated the test set data in [BaiduNetDisk](https://pan.baidu.com/s/1paNUmL_qgzYpVQMyeA4whA?pwd=gy5s).

Example test data for Tasks 1 and 2 in `test-scene_session_task.json`:
```
{"Riverdale_S04E03": [
    {"vid": "Riverdale_S04E03_clip_000", "subs": [
        {"turn_id": 0, "en_text": "What were you doing in there?", "ch_text": "你来里面干什么", "start": 0.0, "end": 1.15},
        {"turn_id": 1, "en_text": "I was... praying.", "ch_text": "我在祈祷", "start": 1.15, "end": 2.9},
        ...]
    },
    ...
]
}
```

In task 1 (Dialogue Scene Identification) and task 2 (Dialogue Segment Identification), you need to predict 0 or 1 as the scene/segment label for each turn.

Your predictions for each task should be formatted as follows: [example.json](https://github.com/yellow-binary-tree/NLPCC-2023-Shared-Task-10/blob/master/example_submission/test-scene_session_random_baseline.json)

```
{
    "Riverdale_S04E03_clip_000": [1, 0, 0, 0, 1, ...],
    "Riverdale_S04E03_clip_001": [0, 0, 1, 0, 0, ...],
    ...
}
```

Example test data for Tasks 3 and 4 in `test-dialog_response_task.json`:
```
{"Riverdale_S04E03": [
    {"vid": "Riverdale_S04E03_clip_000", "subs": [
        {"turn_id": 0, "en_text": "What were you doing in there?", "ch_text": "你来里面干什么", "start": 0.0, "end": 1.15},
        {"turn_id": 1, "en_text": "I was... praying.", "ch_text": "我在祈祷", "start": 1.15, "end": 2.9},
        {"turn_id": 2, "en_text": "__ANSWER__", "ch_text": "__ANSWER__", "start": 1.15, "end": 2.9},
        ...]
    },
    ...
]
}
```

In task 3 (Dialogue Response Retrieval) and task 4 (Dialogue Response Generation), if `en_text` key is set as `__ANSWER__`, you need to replace it with your answer. In task 3, you can choose an reply from a candidate file `test-candidates.json` formatted as below, and in task 4, you need to generate the reply on your own. Note that you only need to choose from / generate English replies, and Chinese dialog is only for reference.

```
{
    "Better.Call.Saul_S03E02_clip_000_turn_003": [{"en_text": "en_text1", "ch_text": "中文文本1"}, {"en_text": "en_text2", "ch_text": "中文文本2"}],
    "Better.Call.Saul_S03E02_clip_001_turn_007": [{"en_text": "en_text1", "ch_text": "中文文本1"}, {"en_text": "en_text2", "ch_text": "中文文本2"}],
    ...
}
```

For task 3, your predictions should be formatted as follows: [example.json](https://github.com/yellow-binary-tree/NLPCC-2023-Shared-Task-10/blob/master/example_submission/test-choice_random_baseline.json)
```
{
    "Better.Call.Saul_S03E02_clip_000_turn_003": 3,
    "Better.Call.Saul_S03E02_clip_000_turn_007": 8,
    ...
}
```

For task 4, your predictions should be formatted as follows: [example.json](https://github.com/yellow-binary-tree/NLPCC-2023-Shared-Task-10/blob/master/example_submission/test-gt_random_baseline.json)
```
{
    "Better.Call.Saul_S03E02_clip_000_turn_003": "Your reply for this turn",
    "Better.Call.Saul_S03E02_clip_000_turn_007": "Your reply for this turn",
    ...
}
```

## Submission and Leaderboard
We use CodaLab as the evaulation platform of this task. This is the [competition homepage](https://codalab.lisn.upsaclay.fr/competitions/13435).

After making a submission, you can see the results of the submission on the [Leaderboard](https://codalab.lisn.upsaclay.fr/competitions/13435#results), or [Participate-Submit/View Results](https://codalab.lisn.upsaclay.fr/competitions/13435#participate-submit_results)-> unfold a submission by clicking '+' -> Download output from scoring step.

## Contact
If you have any questions about this task, please send email to wangyueqian@pku.edu.cn

