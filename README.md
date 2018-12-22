# DeepRDS: A deep learning model was used to predict mRNA structural de-structured degree in *S. cerevisiae* 

![Schematic overview of DeepRSS](https://github.com/atlasbioinfo/DeepRDS/blob/master/DeepRDSModel/fig1.png)

DeepRDS predicts the stability of *in vivo* mRNA structures during translation through a series of in vivo RNA structural features. The model was originally modeled in S. cerevisiae by fitting 130000 mRNA structures with their six features: RD, MFE, INI, GC, POS. DeepRSS is an end-to-end binary classification model that can be divided mRNA structure *in vivo* into two types: strongly de-structured mRNA （SDS） or weak de-structured mRNA (WDS). WDS *in vivo* means that although ribosome unwinded mRNA structure during translation, the structure itself could still fold-back and formed structure. SDS *in vivo* means that it was difficult to form a structure again by ribosomes unwinding during translation. DeepRSS can promote the field of mRNA structural design *in vivo* and the elaboration of mRNA structural functions. In the future, more species and more structural features will be added as the version is updated.

## DeepRDS Versions

* Version 1.0-First version released on 20180922 
* Version 1.1-Revise release on 20190101, deleted feature RPKM.

## DeepRDS model usage

DeepRDS was modeled by Tensorflow. You need to install Python and Tensorflow before use DeepRDS. 

**[Tensorflow version>1.0](https://www.tensorflow.org/)**
**[Python3](https://www.python.org/)**

In the Sample folder, we provide all the files for the exercise DeepRDS to predict. Just simply run the following code to complete the prediction of the in vivo structure of the mRNA in Test0 and output the structure to preTest0.

```bash
    python LoadAndPreDictModel.py DeepRDS.tf Test0 >preTest0
```

## DeepRDS design

DeepRDS is the first attempt to apply a deep learning framework to predict the structure of mRNA in vivo, and the model structure is very simple. The input layer of the five units is followed by a 8-layer fully connected layer, each unit is 512. The activation function of the fully connected layer is ReLU, the activation function of the output is Sigmoid, and the optimization algorithm is Adam. In the future update of DeepRDS, we will adopt more structural data and are trying to use CNN or RNN to predict the structure of the body.

## Folder composition
.
>Raw data: raw mRNA structure data with five features and label.
> DeepRDSModel: DeepRDS model and its schematic overview.
> Sample: An example of how to use DeepRDS for mRNA structure state prediction.
> Scripts: Important Perl and Python scripts used.

### ./RawData/ 

The "./Data/" folder contains ten training sets (Train0-9) and development sets (Dev0-9) for training. These data were generated as follows:

All mRNA structural data (from 135844 mRNA structures) were first randomly shuffled three times

```bash
    cat mRNAstructure | shuf | shuf | shuf >shuffledStructrues
```
and then randomly divided into ten groups.

```bash
    cat shuffledStructrues | split -l 13585 
```

Each group of 9 serves as the training set and 1 as the development set (Dev). The training data or development data of TSV format is as follows:

```
	YDR376W	993	1090	1	-0.868242130762128	0.577981563653556	-1.34552595068809	0.316326530612245	0.703241053342336

	Each column is:
	>1. Gene Name;
	>2. mRNA structure begin position(nt);
	>3. mRNA strucuture end position(nt);
	>4. mRNA structure Label(0 means stable trend, 1 means unstable trend)
	>4. Normalized ln(RD) of five studies;
	>5. Normalized MFE;
	>6. Normalized ln(INI) of five studies;
	>7. GC contents of structure sequence;
	>8. Relative positon of mRNA structure;
```

### DeepRDSModel

The folder contains the DeepRDS model and the schematic overview of DeepRDS modeling.

![Schematic overview of DeepRDS](https://github.com/atlasbioinfo/DeepRDS/blob/master/DeepRDSModel/fig7.png)

By performing a 10-fold cross-validation on a variety of hyperparameters, the final end-to-end DNN model has 9 fully connected layers with 256 cells per layer; the activation functions adopted were ReLU and Sigmoid; the Adam optimization function was adopted to accelerate the training process; and optimization techniques, batch normalization, and early stopping was added to prevent overfitting of the model. The precision of the DeepRDS model reached 99.53%.

### Scripts

The Script folder contains scripts for model training, forecasting, and related data processing.

#### FitModel.py

Python scripts are used to build and train DNN models.

usage: 
```bash
    python FitModel.py TrainSet DevSet LayerNumber TrainingProcessOutput ModelSave
```

eg:	   

```bash
    python FitModel.py Train0 Dev0 5 modelOutLayer5Group0 Layer5Group0.tf
```

>* TrainSet: a Training set, Train0-9
>* DevSet: Development set, Dev0-9
>* LayerNumber: 1 to 10 in this project. How many layers of fully connected layers are being designed in the model?
>* TrainingProcessOutput: The accuracy and loss of each epoch during the model training process will be output to this file.
>* ModelSave : Save final model file.

The code has also been detailed in the comments; you can also modify the parameters in the script according to your needs. What needs to be said is that we use GPU for training, so the batch_size set in the script is 2048.

#### LoadAndPreDictModel.py

You need to install Python 3.X and some Python packages below:

Tensorflow 1.0+ and numpy

Specific installation methods are detailed in our article.
	
	Command line:
    ```bash
        python LoadAndPreDictModel.py DeepRDS.tf Test0 >preTest0
    ```
    
The predict results are in TSV format.
	
	Data format (splice by space):
	
	YDR376W 993 1090 1 0.99978
	
>	Each column is:
>	1. Gene Name;
>	2. mRNA structure begin position(nt);
>	3. mRNA structure end position(nt);
>	4. mRNA structure Label
>	5. Model predict value and our threshold of classification is 0.5.

#### calculateAUC.pl

The Perl script for the area under its ROC curve is obtained from the model's predicted data.

usage:
    ```bash
        perl calculateAUC.pl modelPredictData > output
    ```
#### generateTurningMap.pl

This Perl script is used to convert predicted data into turning map data.

Usage: 
```bash
    perl generateTurningMap.pl predictedFile > output
```


#### acc.pl

	A Perl script to compute accuracy of predict results.
	You need to install 'Perl.'
	
	Command line:
	perl acc.pl predictResult
	
	Echo:
	1999 		   	2000    0.9995
	TruePositive	All		Accuracy
    
    
--------
I am a beginner of deep learning because I found that deep learning can solve the problems in my research perfectly, so I started to understand. If there is a BUG in the project, I hope that you will improve it if you propose it. If you have suggestions for the project or would like to discuss with me, you can send me an email.

My Email is: atlasbioin4@gmail.com
--------
