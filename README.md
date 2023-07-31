# Large-scale-Music-data-Audio-content-based-playlists
A system for generating music playlists based on the results of audio content analysis. The MusAV dataset is used as a music audio collection, music descriptors are extracted using Essentia, and a simple user interface is used to generate playlists based on these descriptors.

## Music collection

MusAV [1] contains a variety of music, with 2,092 30-second track previews covering 1,404 genres.  Download the MusAV dataset from Google Drive. The `audio_chunks` subfolder contains the audio tracks we will use as our music collection. 


## Audio analysis with Essentia

Write a standalone script to analyze the entire audio collection and extract the following descriptors:
Tempo (BPM): you can either use the default settings for the RhythmExtractor2013 signal processing algorithm in Essentia or the TempoCNN ML model.
Music styles: use the Discogs-Effnet model which outputs activation values for 400 music styles.
Voice/instrumental classifier. There are multiple models (based on different audio embeddings, but they are all pre-trained on the same voice/instrumental dataset), select one.
Danceability: you can either use the signal processing Danceability algorithm or the Danceability classifier model.
Arousal and valence (music emotion): use any model pre-trained on the emoMusic dataset, they appear to have the best performance in our previous evaluations [1].

The documentation for each Essenia model (following the links) has code snippets with examples of their use. The signal processing algorithms (RhythmExtractor2013, Danceability) simply require an input audio vector. You can load audio with MonoLoader. The ML models generate predictions on short chunks of audio, resulting in multiple predictions by the same model on different chunks of the same audio track. Therefore, you need to summarize them with averaging (as the simplest strategy). 

Designing your script, keep in mind that you should be able to run this script on any given music collection of any size with any nested folder structure (for example, someone could rerun it on their personal music collection). The script should find and analyze all audio files located inside a given folder. It is up to you to decide the format and file structure to store the analysis results for the music collection.

Note that you might encounter analysis errors on some files (unlikely but happens when running analysis on a very large amount of audio tracks) that would end up with an Essentia exception. Your script should be able to skip such errors if they happen and recover analysis without recomputing the tracks that have already been analyzed if it was interrupted.

It is a nice idea to add a progress bar, for example using tqdm. 

Analyze the MusAV audio collection with your script. 


## Playlist generation

Create a simple UI for playlist generation based on the results of audio analysis.

We propose that you use Streamlit to create a simple webpage that you can run locally on your computer. Streamlit has different options for input widgets and it will interactively recompute results based on any changes to these inputs.

We provide an example of Streamlit code to create playlists based on music style queries: https://drive.google.com/drive/folders/1IxILPD3DbncNBbxpOF589WGMwVXvXf49?usp=share_link

In this demo, we analyzed MusAV with the DiscogsEffnet models. You can extend this code, adding search by the analysis results from the rest of the models, or write your own interface from scratch.

Filtering or ranking by style activations is already implemented in the demo example. Test it with your own analysis results.

For tempo, implement search by tempo, where you can specify a tempo range (min to max BPM).

For voice/instrumental classifier, implement a binary selector that allows you to show only music with or without voice.

For danceability, implement a range selector. In the case of Essentia's signal processing algorithm, its output is within the [0, 3] range. In the case of the classifier, the output value is probability of music being danceable, within [0,1] range.

For arousal and valence, the range of values output by the model is within [1, 9]. Implement a range selector.

If you have alternatives ideas for the UI, go ahead and implement them and describe all your decision and implementation steps.


## Listen to the playlists

Create various playlists using your UI. There is no easy way to embed an entire playlist on a webpage in Streamlit. In our demo example we embed the top 10 tracks in separate audio players. To listen to the entire playlist, we store it to an M3U8 playlist file. Use an external media player that can reproduce M3U8 files (for example, VLC, Strawberry).

Note that the audio analysis models aren’t 100% accurate therefore there will be misclassifications. Discuss some of the issues you observed generating your playlists. Highlight, which analysis models seem to work good for you and which fail.
Deliverable
For this task, provide us with
The code used to generate the features from the audio
The features that you extracted from the audio
The code for the user interface. We should be able to run this on our computer to generate our own playlists.
A short report (~1 page) describing the decisions you took when generating the features and building the interface, along with your personal opinion of the quality of the system in terms of its capability to generate playlists. Include your observations on the quality of the extracted features, including examples of good and bad extracted features that you encountered.

## References

[1] Bogdanov, D., Lizarraga-Seijas, X., Alonso-Jiménez, P., & Serra X. (2022). MusAV: A dataset of relative arousal-valence annotations for validation of audio models. International Society for Music Information Retrieval Conference (ISMIR 2022).

