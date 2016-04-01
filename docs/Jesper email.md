### Date: 31st March 2016: Email reply from Jesper ###
### Variant Caller- Structural Variant ####

There is a couple of variant callers available, these callers use different signals to detect variants. Therefore they also perform differently on different kinds of variants. One important signal is the read depth, this signal is used to detect CNVs, and is efficient even when it's hard to map the exact breakpoint of the variant. CNVnator is a widely used tool that uses the read depth. You can either download and install it via this link http://sv.gersteinlab.org/cnvnator/ there is also a nicer documentation available on github: https://github.com/abyzovlab/CNVnator

CNvnator divides genome into bins and searches for bins that have abnormal coverage. It's up to the user to decide the size of these bins, usually I choose 1000 bases, If a larger bin size is chosen, CNVnator will not be able to detect small events. However, by chosing a too small bin size, the false positive rate will be high and large variatns will be split into what appears to be multiple small variants due to noise.

The other signal used to search for structural variants is the discordant pairs. Using these signals you can detect all possible variants, but you need to be able to map the region of the breakpoint, which is not possible in many cases (especially with the 500 bp insert size libraries). Delly and Manta are two published tools that are available for free.

In general, Manta is very fast, using only 3 cpu hours for a 30 x genome, it also have a high specificity. However it is sometimes a bit tricky to run it depending on how your data was mapped and which reference you used. 

Delly on the other hand is very easy to use, but it's specificity is usually worse than Manta. Instead Delly usually have slightly higher precision. Delly is about 10 times slower than Manta.you can read more about delly here: https://github.com/tobiasrausch/delly

I would recomend you to use CNVnator and pick one of the discordant pair callers. Manta is probably the best choice, to couple with CNVnator, it is fast and is able to detect both big and small variants. If you are not able to run Manta, then Delly is also a good choice.

Lastly Me and Francesco Vezzi at NGI have developed a new variant caller called FindTranslocations, you could download it from github. However it is not yet published : https://github.com/SciLifeLab/FindTranslocations

I use VEP(variant effect predictor) to annotate the structural variants, it works directly out of the box without any special settings. However, since the variants may be very large, I have found that the --per_gene option is important, otherwise the vcf will get very big since many variants will cover multiple genes(some variants may cover hundreds of genes( and transcripts. By using this option you will only get the most severe effect per gene.

Delly, FindTranslocations and Manta uses a filter flag to describe high quality and low quality variants. variants labeled PASS in the filter field are high quality variants. Usually I remove the rest to reduce the false positive rate. The FindTranslocations caller contains internal database functionality, usually we create databases of our samples and study only rare variants. This may not be efficient for you since all your patients are lung patients. However, by running your callers on some public samples(like a few of the 1000 genome samples) you could create a small internal database to filter out reference errors and normal variants. The databse functionality is not limited to FindTranslocations, so you could use it together with the other callers as well.

If you don't want to create your own Structural variant pipeline, there's also a few ones already available. Many of my collegues use speedseq, it is easy to use and combines CNVnator and lumpy. You can find it here:
https://github.com/hall-lab/speedseq

we are using a pipeline called FindSV, you can download it from git hub, it combines findTranslocations and CNVNator:
https://github.com/J35P312/FindSV_core

Most of these tools are available through the UPPMAX module system, so if you are working from uppmax, you will not need to install most of the tools.
