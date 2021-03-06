# Punny Captions: Witty Wordplay in Image Descriptions
This is an implementation of the NAACL '18 paper "Punny Captions: Witty Wordplay in Image Descriptions", by Arjun Chandrasekaran, Devi Parikh & Mohit Bansal. The forward RNN implementation is complete.

The pretrained model for the "Show and Tell" [paper](https://arxiv.org/abs/1609.06647) has been used from [here](https://github.com/KranthiGV/Pretrained-Show-and-Tell-model), along with the original paper implementation files in im2txt from [here](https://github.com/tensorflow/models/tree/master/research/im2txt). Changes have been made primarily to ```im2txt/caption_generator``` and ```im2txt/run_inference``` files. The code for extracting the top-5 object categories predicted by the Inception-ResNet-v2 model has been used from [here](https://github.com/fchollet/deep-learning-models/blob/master/inception_resnet_v2.py). The file ``inception_resnet_v2.py`` has been added to ``im2txt`` folder.

Full text available at: https://arxiv.org/abs/1704.08224

## Implementation Specifics : Modifications to paper
The paper uses the top 5 object categories from the Inception-ResNet-v2 model. We observe that many of these object categories contain multiple words. For example, **meat_loaf** or **chocolate_sauce**. Since the pun dictionary does not account for such words, we initially attempted to go about it by splitting these into separate tags. However, the individual words are not always relevent to the context. This can especially be a problem if their pun counterparts appear in the punny caption with high probability, resulting in the intended punny caption not being very relevant.

We work around this by enforcing a probability filter to the obtained top 5 object categories. We only consider them if the **probability > 0.1**. This ensures that only relevant tags are used for pun generation.

## Resources
1. JSON file containing pun words associated with coco images : [coco_puns.json](https://drive.google.com/open?id=1AKiq2ryxXck_l2kQVqsAgkZKVVaPwarK)
2. Global pun dictionary : [pun_dict](https://drive.google.com/open?id=1mpd8yAMvMeWOgWpn2p1arTUKDRRRFuOt) (python pickle format)
3. Checkpoint file : [model.ckpt-2000000](https://drive.google.com/open?id=1A8pJefdRavYw7OOcdRu3LD1hpHLfUrtM)
4. Coco images folder : http://cocodataset.org/#download
    * Download the 2014 Val images [41K/6GB]

**_NOTE_** : _Be sure to make changes in ```coco_puns.json``` file to reflect the correct location of COCO images if you wish to use it._

## Steps
0. Follow steps for the [Pretrained Show and Tell Model](https://github.com/KranthiGV/Pretrained-Show-and-Tell-model)

1. Add the above ```punny_captions/im2txt``` folder in the same directory

2. Set the variables
```
CHECKPOINT_PATH="/path/to/model.ckpt-2000000"
VOCAB_FILE="/path/to/word_counts.txt"
IMAGE_FILE="/path/to/image-file.jpg"
PUN_DICT="/path/to/pun_dict"
```

3. Build the project
```
bazel build -c opt im2txt/run_inference
```

4. Run the program
```
bazel-bin/im2txt/run_inference \
--checkpoint_path=${CHECKPOINT_PATH} \
--vocab_file=${VOCAB_FILE} \
--pun_dict=${PUN_DICT} \
--input_files=${IMAGE_FILE}
```

## Example
### Input image
<p align="center"><img src="https://user-images.githubusercontent.com/13128829/41868058-923493e8-78d2-11e8-9435-7b4b2c2204f5.jpg"></p><br>

### Output
```
Captions for image test.jpg:
  0) a piece of cake on a plate with a fork . (p=0.005761)
  1) a piece of chocolate cake on a white plate . (p=0.003032)
  2) a piece of chocolate cake on a plate with a fork . (p=0.002271)

Punny Captions for image test.jpg:
  0) peace of cake on a plate with a fork . (logp=-20.962578)
  1) peace of chocolate cake on a white plate . (logp=-21.737056)
  2) peace of chocolate cake on a plate with a fork . (logp=-22.183619)
  3) a peace of chocolate cake on a plate . (logp=-18.170367)
  4) a peace of chocolate cake on a white plate . (logp=-18.171735)
  5) a peace of chocolate cake on a plate with a fork . (logp=-19.184663)
  6) a plate folk with a piece of cake on it . (logp=-24.674716)
  7) a plate folk with a piece of cake on it (logp=-25.122556)
  8) a plate folk with a piece of cake and a fork . (logp=-25.430850)
  9) a slice of peace cake on a plate . (logp=-20.925564)
  10) a slice of peace cake on a plate with a fork . (logp=-20.974435)
  11) a slice of peace cake with a fork on a plate . (logp=-21.497675)
  12) a piece of cake folk on a plate . (logp=-21.508276)
  13) a piece of cake folk on a plate with a fork . (logp=-21.737152)
  14) a piece of cake folk on a plate (logp=-22.297132)

```
**_NOTE_** : _The output is iteration-wise so that the pun word can be observed in the first position for the first three outputs, second position for the next three outputs, and so on._

## Results
| Image | Tag : Puns | Punny Caption |
| --- | --- | --- |
| ![woman_phone](https://user-images.githubusercontent.com/13128829/41987519-061e2c80-7a57-11e8-91b7-08c298b7ecb6.jpg) | (cell : sell) | a woman sell her cell phone on the sidewalk |
| ![bear](https://user-images.githubusercontent.com/13128829/41987734-b600349a-7a57-11e8-8847-202370acda02.jpg) | (bear : bare) | a bare bear is standing on a rock |
| ![zebras](https://user-images.githubusercontent.com/13128829/41987867-18df76ac-7a58-11e8-9ca1-28f52ed68665.jpg) | (herd : heard) | a heard of zebras are standing in a field |
| ![food](https://user-images.githubusercontent.com/13128829/41988460-d06b9ed0-7a59-11e8-859a-a0eff51bd028.jpg) | (white : wight)<br>(meat : meet, mete)<br>(fork : folk) | meet and broccoli on a plate with a fork |
