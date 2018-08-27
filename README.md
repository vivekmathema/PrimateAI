
PrimateAI:  deep residual neural network for classifying the pathogenicity of missense mutations.    

PrimateAI is trained on a dataset of ~380,000 common missense variants from humans and six non-human primate species, using a semi-supervised benign vs unlabeled training regimen.  The input to the network is the amino acid sequence flanking the variant of interest and the orthologous sequence alignments in other species, without any additional human-engineered features, and the output is the pathogenicity score from 0 (less pathogenic) to 1 (more pathogenic).  To incorporate information about protein structure, PrimateAI learns to predict secondary structure and solvent accessibility from amino acid sequence, and includes these as sub-networks in the full model.  The total size of the network, with protein structure included, is 36 layers of convolutions, consisting of roughly 400,000 trainable parameters. 

The method is described in the publication:    
Sundaram, L et al, Predicting the clinical impact of human mutation with deep neural networks.  Nature Genetics 2018. 
https://www.nature.com/articles/s41588-018-0167-z
    

ARCHITECTURE of DEEP LEARNING NETWORK    
The pathogenicity prediction network takes as input the 51-length amino acid sequence centered at the variant of interest, and the outputs of the secondary structure and solvent accessibility networks for each variant.  To represent the variant, the network receives both the 51-length reference amino acid sequence ome and the alternative 51-length amino acid sequence with the missense variant substituted in at the central position.  Three 51-length position frequency matrices (PFMs) are generated from multiple sequence alignments of 99 vertebrates, including one for 11 primates, one for 50 mammals excluding primates, and one for 38 vertebrates excluding primates and mammals.  
The five direct input channels are passed through an upsampling convolution layer of 40 kernels with linear activations. The human reference amino acid sequence (1a) is merged with the PFMs from primate, mammal, and vertebrate multiple sequence alignments (Merge 1a).   Similarly, the human alternative amino acid sequence (1b), is merged with the PFMs from primate, mammal, and vertebrate multiple sequence alignments (Merge 1b).  This creates two parallel tracks, one for the reference sequence, and one with the alternate sequence with the variant substituted in.      
The merged feature map of both reference channel and the alternative channel (Merge 1a and Merge 1b) are passed through a series of six residual blocks (Layers 2a to 7a, Merge 2a and Layers 2b to 7b, Merge 2b). The output of the residual blocks (Merge 2a and Merge 2b) are concatenated together to form a feature map of size (51,80) (Merge 3a, Merge 3b) which fully mixes the data from the reference and alternative channels.  Next, the data has two paths for passing through the network in parallel, either through a series of six residual blocks containing two convolutional layers each, as defined in section 2.1 (Merge 3 to 9, Layers 9 to 46 excluding layer 21,34), or via skip connections, which connect the output of every two residual blocks after passing through a 1D convolution (Layer 21, Layer 37, Layer 47).  Finally, the merged activations (Merge 10) are fed to another residual block (layers 48 to 53, Merge 11).  The activations from Merge 11 are given to a 1D convolution with filter size 1 and sigmoid activation (Layer 54, then passed through a global max pooling layer that picks a single value representing the network’s prediction for variant pathogenicity.    
The secondary structure deep learning network predicts 3-state secondary structure at each amino acid position: alpha helix (H), beta sheet (B), and coils (C).  The solvent accessibility network predicts 3-state solvent accessibility at each amino acid position:  buried (B), intermediate (I), and exposed (E).  Both networks only take the flanking amino acid sequence as their inputs, and were trained using labels from known non-redundant crystal structures in the Protein DataBank.  For the input to the pre-trained 3-state secondary structure and 3-state solvent accessibility networks, we used a single PFM matrix generated from the multiple sequence alignments for all 99 vertebrates, also with length 51 and depth 20.  After pre-training the networks on known crystal structures from the Protein DataBank, the final two layers for the secondary structure and solvent models were removed and the output of the network was directly connected to the input of the pathogenicity model.  The best testing accuracy achieved for the 3-state secondary structure prediction model is 79.86 %.        
Both our deep learning network for pathogenicity prediction (PrimateAI) and deep learning networks for predicting secondary structure and solvent accessibility adopted the architecture of residual blocks. The detailed architectures are shown in figures below.        

![Illustration of PrimateAI network](FigureS4.pdf)       
![Illustration of secondary structure and solvent accessibility networks](FigureS5.pdf)

LICENSE    
Copyright (c) 2018 Illumina, Inc. All rights reserved.

This software is provided under the terms and conditions of the GNU GENERAL PUBLIC LICENSE Version 3.

You should have received a copy of the GNU GENERAL PUBLIC LICENSE Version 3 along with this program. If not, see https://github.com/illumina/licenses/.


INSTRUCTIONS    
The Python script provided includes the deep residual neural network for variant pathogencity estimation, as well as two deep residual neural models for predicting secondary structure and solvent accessibility of amino acids. These models are written using Python Keras package.

To run this script, users need to pre-install Python packages numpy, tensorflow, and keras. In their Python script, they can import this script to adopt PrimateAI neural network models. 


DATA for DOWNLOADING    
Users can download labeled training data on BaseSpace:
https://basespace.illumina.com/s/cPgCSmecvhb4
Or they can prepare their own training, validation, and testing datasets.

Users can also download exome-wide predictions of pathogenicity scores from BaseSpace:
https://basespace.illumina.com/s/cPgCSmecvhb4


