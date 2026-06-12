---
title: Face sorting algorithm for large photo collections
parent: Projects
nav_enabled: false
nav_order: 1
---



# Appetizer
{: .no_toc }
{: .d-inline-block }
06-12-2026
{: .label .label-green }

I went on a vacation with friends.
One week in, we generated more than 1,600 photos across eight camera devices.
Without wanting to explicitly review all the data, I wanted to extract the photos that contained me and send them to my family and friends, who might be concerned with how I perceived the vacation, not so much how others perceived it.
While using face recognition algorithms, we rediscover a well-known L-curve property along the way.
Let's jump in.
{: .fs-6 .fw-300 }

---
## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}
---

# Introduction
Modern vacations are no longer documented by one person carrying one camera.
They are documented by everyone, all the time, across phones, action cameras, compact cameras and the occasional questionable screenshot.
That is great while travelling and rather annoying once the photos need to be sorted.
The resulting task is simple to describe: given a large unordered folder of photos, extract the subset that likely contains one target person.

This is not quite the same as finding all faces.
A photo with five people is useful only if one of those faces is the target person.
A landscape photo is not useful at all.
A blurry side profile may still be useful, while a sharp photo of another person is not.
Therefore, the problem is not object detection alone, but identity matching under imperfect data.

Manually solving the task scales badly.
For 50 photos, manual review is fine.
For more than 1,600 photos, manual review turns into data cleaning, and data cleaning is where automation should carry the first load.
The goal of the project is therefore practical rather than academic:
build a local script pipeline that identifies likely positives, separates obvious negatives, and leaves only the ambiguous middle for optional human review.

The full code is available in my GitHub repository [facesorter](https://github.com/auerbenji/facesorter).
The repository is intentionally small.
It does not try to be a photo management app.
It is a local workflow that turns a reference folder and a data folder into a ranked score file and, finally, into an exported folder of likely matching photos.

# Model
The model uses [InsightFace](https://github.com/deepinsight/insightface) through the `FaceAnalysis` interface and the `buffalo_l` model.
The scripts run locally, support common phone image formats such as `.jpg`, `.jpeg`, `.heic`, `.heif`, and `.hif`, and apply EXIF orientation before face detection.
This matters in practice because phone photos often store rotation as metadata rather than as already-rotated pixels.

From a user's point of view, the workflow has three acts: calibration via `calibrate.py`, scoring via `score.py` and export via `export_threshold.py`.
The helper script `visualization.py` sits between scoring and export, because the threshold should be chosen after looking at the score distribution.

## Calibration
`calibrate.py` builds the reference identity.
I placed 10-20 photos of the target person into a `calibration/` folder.
The photos should cover front views, side views, different lighting and small appearance changes such as cap or no cap.
Sunglasses are not helpful, unless sunglasses are also expected in the target image set.

For each calibration photo, the script detects faces and keeps the largest detected face.
This is a simple but effective assumption for reference photos, because calibration images should show the target person clearly.
The selected face is converted into a normalized embedding vector and stored in `out/identity_person.npz` together with a small calibration report.
The script keeps the set of reference embeddings instead of collapsing them into one average vector.
This preserves some variation from the calibration photos, which is useful when the target person appears under different angles.

{: .note-title }
> Face embeddings
>
> A face embedding is a numerical representation of a face.
> In this workflow, InsightFace maps each detected face to a normalized vector.
> Similar faces should point in a similar direction in vector space.
> This turns identity matching into a vector comparison problem.

## Scoring
`score.py` iterates through all photos in the `data/` folder.
For each image, it detects all faces and calculates the similarity between every detected face and every stored calibration embedding.
Because the embeddings are normalized, cosine similarity reduces to a dot product.
The score of a photo is the best match found in that image:

$$
s_i = \max_{f \in F_i, r \in R} f^\top r
$$

where $$F_i$$ is the set of detected face embeddings in photo $$i$$ and $$R$$ is the set of reference embeddings from calibration.
If no face is detected, the script assigns a score of $$-1$$.
This convention is convenient because photos without detected faces collect at the bottom of the sorted score curve.

The output is a `scores.csv` file with the image path, score, number of detected faces, status and possible error message.
The script also supports resume mode, which is useful because scoring is the expensive part of the workflow.
In my setting, the runtime was still reasonable, but expensive enough that I did not want to repeat it after every small threshold experiment.

## Threshold export
`export_threshold.py` does not run face recognition again.
It reads `scores.csv`, selects all photos above a threshold, and copies them into an output folder while preserving the original folder structure.
It can also export a score window, for example all photos with scores between `0.00` and `0.50`.
This is useful when separating high-confidence positives from a second folder that deserves human review.

The model is therefore not a binary classifier in the strict sense.
It is a ranker.
The threshold is a user decision, and the best threshold depends on what is more costly: missing a good photo or manually deleting a false positive.

# Results and discussion
[Figure 1](#fig-l-curve) shows the resulting score distribution in decreasing order.
The horizontal axis represents the photo rank after sorting by score.
The vertical axis represents the best identity score found in each photo.
High values indicate that at least one detected face is similar to the calibration identity.
Values of $$-1$$ indicate that no face was detected.

<figure id="fig-l-curve">
  <img src="/assets/images/L-curve.svg" alt="Face sorter L-curve">
  <figcaption><strong>Figure 1:</strong> Face sorter L-curve: sorted identity scores.</figcaption>
</figure>

The curve has the desired shape.
The first region contains high-confidence photos that are likely to show the target person.
The middle region contains photos with at least one detected face, but lower confidence.
This is the noisy part: it includes side views, small faces, occlusions, other people with weak similarity, and photos where the model is uncertain for understandable reasons.
The final flat region at $$-1$$ contains photos without detected faces, most likely landscape photos or object photos.

The vertical cutoff in the plot indicates that approximately 41% of the image set is probably landscape.
That is already valuable.
Almost half of the review burden can be removed without making an identity decision at all.
The remaining curve then gives a practical thresholding strategy:
export the high-confidence region for direct use, export the middle region only if recall matters, and discard or archive the $$-1$$ region.

In qualitative review, the high-score output was good enough to solve the original problem.
The extracted folder contained the photos I was looking for, without forcing me to inspect the complete raw collection.
The lower-score region was useful but not clean.
It should be treated as a review queue, not as a final album.
This is exactly where the L-curve helps: it does not promise a universally correct threshold, but it shows where confidence changes and where manual attention is still worth spending.

The main limitation is the missing ground truth.
I did not label all vacation photos by hand, so I cannot report precision, recall or an ROC curve.
For the purpose of the project, this is acceptable.
The goal was not to prove a new face recognition model.
The goal was to turn a large private photo dump into a small useful folder.
On that criterion, the script pipeline works.

**In summary**: face sorting can be treated as a ranking problem.
A small calibration set defines the identity, InsightFace scores all detected faces, and the L-curve turns the otherwise arbitrary threshold into an informed decision.
The workflow is simple, local and good enough to remove the boring part of photo review.
