# Speech Recognition Tutorials
------------

In this repository, you will find several tutorials discussing what is Automatic Speech Recognition (ASR), general concepts, specific models and multiple sub-domains of ASR such as Speech Classification, Voice Activity Detection, Speaker Recognition, Speaker Identification and Speaker Diarization.


------------

# Automatic Speech Recognition

1) `ASR_with_NeMo`: Discussion of the task of ASR, handling of data, understanding the acoutic features, using an Acoustic Model and train on an ASR dataset, and finally evaluating the model's performance.

2) `ASR_with_Subword_Tokenization`: Modern ASR models benefit from several improvements in neural network design and data processing. In this tutorial we discuss how we can use Tokenizers (commonly found in NLP) to significantly improve the efficiency of ASR models without sacrificing any accuracy during transcription.

3) `ASR_CTC_Language_Finetuning`: Until now, we have discussed how to train ASR models from scratch. Once we get pretrained ASR models, we can then fine-tune them on domain specific use cases, or even other languages ! This notebook discusses how to fine-tune an English ASR model onto another language, and discusses several methods to improve the efficiency of tranfer learning.

4) `Online_ASR_Microphone_Demo`: A short notebook that enables us to speak into a microphone and transcribe speech in an online manner. Note that this is not the most efficient way to perform streaming ASR, and it is more of a demo.

5) `Offline_ASR`: ASR models are able to transcribe speech to text, however that text might be inaccurate. Here, we discuss how to leverage external language models build with KenLM to improve the accuracy of ASR transcriptions. Further, we discuss how we can extract time stamps from an ASR model with some heuristics. 

6) `Streaming_ASR`: Some ASR models cannot be used to evaluate very long audio segments due to their design. For example, self attention models consume quadratic memory with respect to sequence length. For such cases, this notebook shows how to perform streaming audio recognition in a buffered manner.

7) `ASR_for_telephony_speech`: Audio sources are not homogenous, nor are the ways to store large audio datasets. Here, we discuss our observations and recommendations when working with audio obtained from Telephony speech sources.


8) `Online_Noise_Augmentation`: While academic datasets are useful for training ASR model, there can often be cases where such datasets are prestine and dont really represent the use case in the real world. So we discuss how to make the model more noise robust with Online audio augmentation.

9) `Intro_to_Transducers`: Previous tutorials discuss ASR models in context of the Connectionist Temporal Classification Loss. In this tutorial, we introduce the Transducer loss, and the components of this loss function that are constructed in the config file. This tutorial is a prerequisite to the `ASR_with_Transducers` tutorial.

10) `ASR_with_Transducers`: In this tutorial, we take a deep dive into Transducer based ASR models, discussing the similarity of setup and config to CTC models and then train a small ContextNet model on the AN4 dataset. We then discuss how to change the decoding strategy of a trained Transducer from greedy search to beam search. Finally, we wrap up this tutorial by extraining the alignment matrix from a trained Transducer model. 

11) `Self_Supervised_Pre_Training`: It can often be difficult to obtain labeled data for ASR training. In this tutorial, we demonstrate how to pre-train a speech model in an unsupervised manner, and then fine-tune with CTC loss.

----------------

# Speech Command Recognition

1) `Speech_Commands`: Here, we study the task of speech classification - a subset of speech recognition that allows us to classify a spoken sentence into a single label. This allows to speak a command and the model can then recognize this command and perform an action.

2) `Online_Offline_Speech_Commands_Demo`: We perform a joint online-offline inference of speech command recognition. We utilize an online VAD model to detect speech segments (whether audio is in fact speech or background), and if speech is detected then a speech command recognition model classifies that speech in an offline manner. Note that this demo is a demonstration of a possible approach and is not meant for large scale use.

3) `Voice_Activity_Detection`: A special case of Speech Command Recognition - where the task is to classify whether some audio segment is speech or not. It is often a tiny model that is used prior to a large ASR model being used.

4) `Online_Offline_Microphone_VAD_Demo`: Similar to before, we demo an online-offline inference of voice activity detection. We discuss metrics for comparing the performance of streaming VAD models, and how one can try to perform streaming VAD inference with a microphone. Note that as always, this demo is a demonstration of a possible approach and is not meant for large scale use.
