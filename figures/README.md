\# Interpretable Machine Learning for Subjective Audio Quality Prediction



This project investigates whether subjective audio quality ratings can be predicted from measurable acoustic characteristics of audio signals.



The work was developed as a practical introduction to Audio Machine Learning, with a particular interest in transferring the methodology to automotive audio and NVH applications.



\---



\## 1. Project Objective



Subjective audio quality assessment usually relies on human listeners.



The objective of this project is to investigate whether acoustic signal characteristics can be used to approximate human quality ratings using machine learning.



Two approaches were evaluated:



1\. \*\*No-reference prediction\*\*

&#x20;  - Only the processed audio signal is available.



2\. \*\*Reference-based prediction\*\*

&#x20;  - The processed signal is compared with its corresponding reference signal.



\---



\## 2. Dataset



The project uses the \*\*ODAQ (Open Dataset of Audio Quality)\*\*.



The dataset contains:



\- 30 original audio contents

\- 8 conditions per content

\- 240 audio stimuli

\- 26 subjective ratings per stimulus

\- 6240 individual human ratings



The mean subjective rating of each stimulus was used as the regression target.



To avoid content leakage, all processed versions of the same original audio were kept in the same cross-validation group.



\---



\## 3. Audio Signal Exploration



Several signal representations were explored before modeling:



\- Waveform

\- FFT

\- STFT spectrogram

\- Mel spectrogram

\- MFCC



The analysis showed that low-quality signals can exhibit major spectral modifications.



For example, a low-pass degraded signal showed a strong loss of high-frequency content, while higher-quality signals preserved a richer spectral structure.



However, spectral bandwidth alone was not sufficient to explain subjective quality.



\---



\## 4. Acoustic Feature Extraction



Each audio file was converted into a fixed-size numerical feature vector.



Features included:



\- RMS energy

\- Zero Crossing Rate

\- Spectral Centroid

\- Spectral Bandwidth

\- Spectral Rolloff

\- 13 MFCC coefficients



For each frame-level feature, the mean and standard deviation over time were calculated.



This resulted in:



\*\*36 acoustic features per audio stimulus.\*\*



\---



\## 5. No-Reference Modeling



The first experiment used the 36 absolute acoustic features.



Models evaluated:



\- Dummy Regressor

\- Random Forest

\- XGBoost



Evaluation was performed using \*\*GroupKFold\*\*, with the original audio content used as the grouping variable.



This ensured that the model was tested on audio contents that were completely unseen during training.



\### Initial results



| Model | MAE | RMSE | R² |

|---|---:|---:|---:|

| Dummy Regressor | 24.37 | 27.95 | -0.006 |

| Random Forest | 16.47 | 22.61 | 0.328 |

| XGBoost | 17.13 | 23.53 | 0.273 |



The Random Forest clearly outperformed the naive baseline.



However, error analysis revealed strong content dependency.



For some unseen signals, the model confused natural spectral characteristics of the source content with audio degradation.



\---



\## 6. Reference-Based Feature Engineering



To reduce content dependency, reference-relative features were introduced.



For each feature:



Delta feature:



`feature\_processed - feature\_reference`



This representation measures how the processing modifies the original signal.



The reference-based model significantly improved generalization.



\### Processed signals only



Using 36 delta features:



| Representation | MAE | RMSE | R² |

|---|---:|---:|---:|

| Absolute features | 14.40 | 19.85 | 0.245 |

| Delta features | 10.54 | 15.20 | 0.565 |



This suggests that relative acoustic changes are more informative for quality prediction than absolute spectral characteristics.



\---



\## 7. Enhanced Reference-Based Representation



An additional set of features was introduced:



`abs(feature\_processed - feature\_reference)`



These features explicitly represent the \*\*magnitude of the acoustic modification\*\*, independently of its direction.



The final representation therefore contained:



\- 36 directional delta features

\- 36 absolute delta features



Total:



\*\*72 features\*\*



The Random Forest was optimized using nested grouped cross-validation.



\### Final performance



| Metric | Result |

|---|---:|

| MAE | \*\*10.00\*\* |

| RMSE | \*\*13.96\*\* |

| R² | \*\*0.635\*\* |



The improvement demonstrates that feature representation had a larger impact on performance than simply changing the regression algorithm.



\---



\## 8. Model Explainability



SHAP was used to understand the final Random Forest model.



Important features included:



\- Absolute change in MFCC1 mean

\- Absolute change in spectral rolloff

\- MFCC3 temporal variability

\- Absolute change in spectral centroid



SHAP analysis showed that large deviations from the reference signal generally reduced predicted audio quality.



The model uses both:



\- the \*\*direction\*\* of acoustic changes

\- the \*\*magnitude\*\* of acoustic changes



Local SHAP waterfall plots were also used to explain individual predictions.



\---



\## 9. Main Findings



The main conclusions of this project are:



\- Acoustic descriptors contain useful information about subjective audio quality.

\- A single spectral feature is insufficient to predict human perception.

\- No-reference prediction is strongly affected by source-content variability.

\- Reference-relative features substantially improve generalization.

\- The magnitude of spectral changes is particularly informative.

\- Feature engineering produced a larger improvement than switching from Random Forest to XGBoost.

\- Some extreme-quality and degradation conditions remain difficult to predict.



\---



\## 10. Limitations



This project has several limitations:



\- The dataset contains only 30 original audio contents.

\- The current representation relies on aggregated handcrafted features.

\- Temporal artifacts may not be fully represented by mean and standard deviation statistics.

\- The reference-based approach requires access to the original reference signal.

\- The dataset is focused on general audio quality rather than automotive NVH.



Future work could investigate:



\- Spectral contrast

\- Spectral flatness

\- Spectral flux

\- Frame-level reference distances

\- Mel-spectrogram distances

\- Learned audio embeddings

\- Deep learning approaches

\- Larger automotive-specific subjective datasets



\---



\## 11. Relevance to Automotive Audio / NVH



The methodology can be transferred to automotive audio engineering.



A possible automotive workflow would be:



Vehicle audio measurements  

→ Signal processing  

→ Acoustic feature extraction  

→ Machine learning  

→ Prediction of expert subjective ratings  

→ Explainability



Such models could support engineers by identifying acoustic characteristics associated with subjective perception and potentially reducing the number of repeated jury evaluations required during tuning iterations.



The objective is not to completely replace expert listening, but to provide a data-driven decision-support tool during engineering development.



\---



\## Project Structure



```text

audio-quality-ml/

│

├── data/

│

├── models/

│

├── figures/

│

├── notebooks/

│   ├── 01\_data\_exploration.ipynb

│   ├── 02\_feature\_extraction.ipynb

│   ├── 03\_modeling.ipynb

│   ├── 04\_reference\_based\_model.ipynb

│   └── 05\_explainability.ipynb

│

├── src/

├── requirements.txt

└── README.md

