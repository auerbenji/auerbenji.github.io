---
title: Face sorting algorithm for large photo collections
parent: Projects
nav_enabled: true
nav_order: 1
---



# Appetizer
{: .no_toc }
{: .d-inline-block }
06-12-2026
{: .label .label-green }

I went on a vacation with friends. One week in, we generated +1,500 photos across eight camera devices including iPhones, Pixel phones, proper DSLR cameras - shooting in JPG, HEIF and of course containing some WhatsApp photos usually available as .png. Post to the vacation we bundled +20GB of photos on a G-Drive folder: a complete mess by file name, kind, date and of course content. I was wondering - what is the best way to sort out this 'mess' and get to the photos that matter to me the most as quickly as possible?

While I was pondering the question 'what photos do actually matter the most', I received a call by my grandmother: 'grandson, how was your vacation?'. I relized that while the photos that matter to me, the photos mattering to my family and friends, would of course, be photos containing me doing things. So the goal was simple: without wanting to explicitly review all the data, I wanted to extract the photos that contained me and send them to my family and friends.

This work shows how to calibrate a face recognition tool with 10-15 photos that can simply be obtained from any modern photo library. Next, it shows how a scoring algorithm provides a one-time scoring step writing information about the quality of the scoring into a `.csv` file. Since step is expensive we only run it once. A visualization script provides a graphical insight in the quality of the scoring step.
With the obtained insight, we then cut out the photos with desired score-quality.

For the readers that are familiar with Tikhonov regularization and regression analysis: we will rediscover a slope whose look is resembling the shape of the L-curve that is used as a parameter choice technique for the Tikhonov method. Sounds exciting doesn't it? Let's jump in.


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

Manually solving the task scales badly.
For 50 photos, manual review is fine.
For +1,500 photos, manual review turns into data cleaning, and data cleaning is where automation should carry the first load.

The goal of the project is therefore practical rather than academic:
build a local script pipeline that identifies likely positives, separates obvious negatives, and leaves only the ambiguous middle for optional human review.

This is not quite the same as finding all faces.
A photo with five people is useful only if one of those faces is the target person.
A landscape photo is not useful at all.
A blurry side profile may still be useful, while a sharp photo of another person is not.
Therefore, the problem is not object detection alone, but identity matching under imperfect data.

The work targets a practical human-governed workflow:
- generate a reusable and interchangable identify file that contains the person of interest
- score all data once giving insight in sucess of the algorithm, number of faces detected and the confidence score
- generate a graphical representation of the scoring
- with that graphical insight let the operater decide from which score on to extract the photos

The full code is available in my GitHub repository [facesorter](https://github.com/auerbenji/facesorter).
Note that by storing the `identity.npz` file the facesorter turns into a reusable tool running two scripts for every person of interest making it possible to export photo of large group trips, old family-photo hard drives and business trips alike.

# Model
The model uses [InsightFace](https://github.com/deepinsight/insightface) through the `FaceAnalysis` interface and the `buffalo_l` model.
The scripts run locally, parallel, support common image formats such as `.jpg`, `.jpeg`, `.heic`, `.heif`, and `.hif`, and apply EXIF orientation before face detection. This matters in practice because phone photos often store rotation as metadata rather than as already-rotated pixels.
The scripts make us of the package `pillow-heif` to preprocess modern compressed image formats.

The workflow follows the simple steps:
- load calibration data and raw data in the repository
- calibrate via `calibrate.py`
- score via `score.py`
- visualize via `visualization.py`
- export via `export_threshold.py`.

## Calibration
`calibrate.py` builds the reference identity.
I placed 10-20 photos of the target person into a `calibration/` folder.
The photos should cover front views, side views, different lighting and small appearance changes such as cap or no cap, beard or no beard.
Sunglasses are not helpful, unless sunglasses are also expected in the target image set.

The selected face is converted into a normalized embedding vector $$I$$ and stored in `out/identity_person.npz` together with a small calibration report.
The script keeps the set of reference embeddings instead of collapsing them into one average vector.
This preserves some variation from the calibration photos, which is useful when the target person appears under different angles.

{: .note-title }
> Face embedding and similarity
>
> A face embedding is a numerical representation of a face.
> In this workflow, InsightFace maps each detected face to a normalized vector.
> Similar faces should point in a similar direction in vector space.
> This turns identity matching into a vector comparison problem.
> Direction in vector space is also known as cosine similarity:
> The closer the dot product is to the value of '1' the greater is the fit.
>
> For two non-zero vectors cosine similarity is defined as the cosine of the angle $$\theta$$ between them.
> 
> Let $$\mathbf{I}$$ be the identity vector from calibration and $$\mathbf{f_c}$$ a face vector candidate from a photo $$P$$, then the candidate's score $$S_c$$ is obtained via cosine similarity:
>
> $$
> S_c(\mathbf{I}, \mathbf{f_c}) := \cos(\theta)
> = \frac{\mathbf{I} \cdot \mathbf{f_c}}{\lVert \mathbf{I} \rVert \lVert \mathbf{f_c} \rVert}
> $$
> 
> As stated the vectors are already normalized, thus
> 
> $$
> S_c(\mathbf{I}, \mathbf{f_c})
> = \frac{\sum I f_c}
> {1 \cdot 1}
> $$
> 
> and thus
> 
> $$
> \lim_{\theta \to 0^{\circ}} S_c(\mathbf{I}, \mathbf{f_c})
> = 1
> $$
>
> that is exactely the scoring quality returned by `score.py`

## Scoring
`score.py` iterates through all photos in the `data/` folder.
For each image, it detects all faces and calculates the similarity between every detected face and the stored calibration identity.
The score of a photo is the best match found in that image:

$$
S_{P,j} = \max_{f_c \in P_j} S_c
$$

where $$f_c$$ is the set of detected face embeddings in photo $$P_j
$$ and  $$ j \in J$$ is the set of photos placed in the `data/` folder.
If no face is detect, the script assigns a score of $$-1$$.
This convention is convenient because photos without detected faces collect at the bottom of the sorted score curve. The output is a `scores.csv` file with the image path, score, number of detected faces, status and possible error message.

## Threshold export
`export_threshold.py` does not run face recognition again.
It reads `scores.csv`, selects all photos above a threshold, and copies them into an output folder while preserving the original folder structure.
It can also export a score window, for example all photos with scores between `0.00` and `0.50`.
This is useful when separating high-confidence positives from a second folder that requires human review.

# Results and discussion
[Figure 1](#fig-l-curve) shows the resulting score distribution in decreasing order.
The abscissa represents the photos $$j$$ in the dataset $$J$$.
The ordinate represents the identity score $$S_{P,j}$$ found in each photo.

<figure id="fig-l-curve">
  <img src="/assets/images/L-curve.svg" alt="Face sorter L-curve">
  <figcaption><strong>Figure 1:</strong> Face sorter L-curve: sorted identity scores.</figcaption>
</figure>

The first region (blue) contains high-confidence photos that are likely to show the target person.
The middle region (orange) is defined as values $$\leq125\%$$ the mean of all $$S_{P,j}$$ that are non-zero.
The last region (green) holds photos without persons at all, indicating landscape photos.
The vertical cutoff in the plot indicates that approximately 41% of the image set is landscape, indicating that almost half of the review burden can be removed without making an identity decision at all.
This leads to a practical review heuristic:
**First**, export landscape alone through a threshold of maximum 0.0.
**Second**, export high quality photos for direct use.
**Third**, cut out the uncertain middle section (orange color) for human review.
I used the simple $$\leq125\%$$ heuristic to color grade the figure.
Taking a closer look we are observing a change in slove curvature right at the color change.
We can identify this region by pure looking but we can also define it:

{: .note-title }
> Signed curvature
>
> Since the dataset is sorted in a descending order during preprocessing thus holding a monotonic score $$S_{C,j}$$, the image index $$j$$ can be used as the discrete curve parameter, as long as the curve is convex (which it is until the cutoff region (transition orange to green)):
> 
> $$
> j = 1,\dots,N,
> \qquad
> s_1 \geq s_2 \geq \dots \geq s_N .
> $$
> 
> The L-curve is therefore represented as a discrete parametric curve
> 
> $$
> \begin{pmatrix}
> x_j \
> y_j
> \end{pmatrix},
> \qquad j = 1,\dots,N .
> $$
> 
> The local tangent angle can be approximated by finite differences:
> 
> $$
> \arctan\left(
> \frac{y_{j+1}-y_j}{x_{j+1}-x_j}
> \right).
> $$
> 
> The corresponding discrete arc-length increment is
> $$
> \sqrt{(x_{j+1}-x_j)^2 + (y_{j+1}-y_j)^2}.
> $$
> 
> The local curvature is then approximated by the change in tangent angle per unit arc length:
> 
> $$
> \kappa_j
> \approx
> \frac{\varphi_{j+1}-\varphi_j}{\Delta s_j}.
> $$
> 
> The transition point is selected at the rank of maximum curvature:
> 
> $$
> \arg\max_j \kappa_j .
> $$
> 
> The cutoff for human review is the corresponding preprocessing score at that ranked image:
> 
> $$
> \tau^\star = s_{j^\star}.
> $$
> 
> Thus, the sorted image index identifies the critical transition rank, while the actual export threshold is recovered from the score value associated with that rank.


# Conclusion

Facesorter generates a reusable identity file.
With that identify, all photos are scored once and scoring information is written into a `.csv` file.
A visualization reveals an L-curve shape that indicates the cut-off between photos containing people and pure landscape. Furthermore, it holds the required information which photos are prone to errors and need manual review.
Lastly, we showed a three step working order to gather the photos.
And lastly, my grandmother just received a best of folder of a few not-hand-picked photos of my last vacation.
