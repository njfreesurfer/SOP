---
title: "Spinal Cord DTI Analysis SOP"
author: "Tony Jiang"
date: "March 2, 2016"
version: "1.0"
---
# Spinal Cord DTI Analysis SOP
This Standard Operation Procedure defines the workflow to do ROI based spine DTI analysis.


## Download DICOM Files
### Log onto DICOM server
1. Open an internet browser and open https://dicom.kesslerfoundation.org/webdb

2. Log in using your credentials (ask IT for help if need)

![](images/login.PNG?raw=true)
3. Locate  the scan from the calendar, click once on that scan
![](images/scan_calendar.PNG?raw=true)

4. Select all the series
![](images/savebuton.PNG?raw=true)
5. save them as in DICOM, single folder.
![](images/saveas.PNG?token=AO-GO0IQOCFB98qZ8S0c_Tcw7i7e9niWks5W4FsPwA%3D%3D)

6. Files will be pushed to scratch drive, transfer to your local workstation
Ask IT for help with mounting scratch drive on your windows computer. If you are on a linux system, do a SSH session to MRI13102, then CD into /mnt/LABS/common/scratch/nic/HPEL/ If you run into permission problem, ask IT for help.

Note: You must log on from a computer which has intranet access to KF sites.

## Reorganize DICOM Files
The DICOM files downloaded from imaging center server are stored in one single folder, we need to reorganize them into different folders based on the series (just as you see from step 4). We do not want to use the option in step 5 because if something happens, we may never find out what was going on.

And this is what the script [reorganizeDICOM.py](https://github.com/njfreesurfer/personal/blob/master/research/SOP/reorganizeDICOM.py) does:

## Design matrix
Locate the DTI series by following the old entires in this [design matrix](https://github.com/njfreesurfer/personal/blob/master/research/grant/NJCSCI_SpinalDTI_2015/DM.csv). This is the roadmap to guide the analyais script to process the reorganized DICOM data. In the future, it should be able to handle both brain and spine data.
## Preprocess data
Run [veryfyDM.sh](https://github.com/njfreesurfer/personal/blob/master/research/grant/NJCSCI_SpinalDTI_2015/scripts/verifyDM.sh) to verify we have all the data available.

A typical scan has 31 DICOM files in each session. You should see output like this:
```shell
Surveying location for  A0023,20160202,HPEL_Jiang,spine,DTI,1,26-spine_ep2d_DTI_TRA_30_p2_strong_dist_0_1
Data available! Total       31 DICOM files located!
Surveying location for  A0023,20160202,HPEL_Jiang,spine,DTI,2,29-spine_ep2d_DTI_TRA_30_p2_strong_dist_0_2
Data available! Total       31 DICOM files located!
Surveying location for  A0023,20160202,HPEL_Jiang,spine,DTI,3,32-spine_ep2d_DTI_TRA_30_p2_strong_dist_0_3
Data available! Total       31 DICOM files located!
Surveying location for  A0023,20160202,HPEL_Jiang,spine,DTI,4,35-spine_ep2d_DTI_TRA_30_p2_strong_dist_0_4
Data available! Total       31 DICOM files located!
```
## Folder structure

You should set up your folder as following (this includes the files after processing)

###### parent folder
- DICOM
  * subj1
    * KESSLER_SKYRA  (Scanning Site)
      * 20140702    (Scan Date)
        * HPEL_Jiang  (Protocol)
          * 15-spine_ep2d_DTI_TRA_30_p2_strong_dist_0/ (series)
  * subj2
  * subj3
- analysis
  * subj1
    * 20140702
      * session1
        * bvals
        * bvecs
        * bvecs_T
        * dtk_AD.nii.gz
        * dtk_MD.nii.gz
        * dtk_RD.nii.gz
        * dtk_adc.nii.gz
        * dtk_fa.nii
        * dtk.trk
        * ROI_mid-C1.nii
        * ...
        * ROI.scene
      * session2
    * 20150401
  * subj2
  * subj3
- scripts
  * processDTI.sh
  * verifyDM.sh
  * ROI_inventory.csv
  * DM.csv
-

If everything looks ok, go ahead and run [processDTI.sh](https://github.com/njfreesurfer/personal/blob/master/research/grant/NJCSCI_SpinalDTI_2015/scripts/processDTI.sh)
After Design matrix file is prepared,now we can move forward with the preprocessing.

## ROI slice location
You should have asked Stephanie to get a couple of screenshots which show the placement of the slices for the DTI data. Each session has one screenshot. Use this screenshot as a visual guide (you need to count the slices in this case). Record the numbers as you move along. DOUBLE CHECK TO MAKE SURE YOU FIND THE BEST SLICE TO REPRESENT THE LEVLE WE ARE MEASURING.

As of 03/02/2016, we analyze total 37 ROIs from C1-T12.
As we draw the ROIs, we need to keep a record of which session data we used to draw the ROIs and the z slice number. This is very important and please keep a hard copy of this table in your binder.

All ROIs should be recorded in ROI_inventory.csv in scripts folder.
```shell
subject,group,scandate,session,roi_label
A0017,SCI,20160107,1,mid-C1
A0017,SCI,20160107,1,C1-C2
A0017,SCI,20160107,1,mid-C2
A0017,SCI,20160107,1,C2-C3
A0017,SCI,20160107,1,mid-C3
...
```

For example, first row means: a ROI named ROI_mid-C1.nii.gz is defined in session1 for the DTI scan acquired on 20160107 for subject A0017. To draw the ROI, you need load the dtk.trk file in trackVis and load dtk_fa_color.nii as a reference image.


No.|Level|session|z-slice
-------|-------|--------|---------
1|mid-C1||
2|C1-C2||
3|mid-C2||
4|C2-C3||
5|mid-C3||
6|C3-C4||
7|mid-C4||
8|C4-C5||
9|mid-C5||
10|C5-C6||
11|mid-C6||
12|C6-C7||
13|mid-C7||
14|C7-T1||
15|mid-T1||
16|T1-T2||
17|mid-T2||
18|T2-T3||
19|mid-T3||
20|T3-T4||
21|mid-T4||
22|T4-T5||
23|mid-T5||
26|T6-T7||
27|mid-T7||
28|T7-T8||
29|mid-T8||
30|T8-T9||
31|mid-T9||
32|T9-T10||
33|mid-T10||
34|T10-T11||
35|mid-T11||
36|T11-T12||
37|mid-T12||


## Drawing ROI in trackVis
Open trackVis, load the track file (you should have multiple sessions, start from the first session) and its *dtk_fa_color* file. Use your table created in previous step, navigate to the z-slice ,draw a new ROI, then save it by naming the new file as for example "ROI_mid-C1" for mid-C1 level. Draw a new ROI for each level in that session. Then save the scene file as *ROI.scene* in that session directory.

## Get ready for ROI analysis
Again, we need to record the whereabout of the ROI files we made in previous step so later on, we know where to pull them out to run ROI analysis.
There is one unified csv file to store all subject's information. You can use your own file, but should follow the format below:
```
subject,group,scandate,session,roi_label
A0017,SCI,20160107,1,mid-C1
A0017,SCI,20160107,1,C1-C2
A0017,SCI,20160107,1,mid-C2
A0017,SCI,20160107,1,C2-C3
A0017,SCI,20160107,1,mid-C3
A0017,SCI,20160107,1,C3-C4
A0017,SCI,20160107,1,mid-C4
A0017,SCI,20160107,1,C4-C5
A0017,SCI,20160107,1,mid-C5
...
 ```
We only need to specify the session number, scan date, subject ID, group(this is useful for group analysis, do whatever you would like), be careful with the spelling of ROI names. It's case sensitives. A better practice is to copy from previous records and modify the session numbers. If you are smart, there are many ways to automate this task. It's really a trivial one so I didn't put together a script to do so. I recommend to use vim to edit the file then use search/replace function to add a new subject.
> 37yy : yank 37 lines from previous subject
 :5,12s/foo/bar/g : change each ""foo" to "bar" fro all lines from line 5 to line 12(inclusive)

Alternatively, you can create a new csv file for each subject per scan. but name it in the convention of A00XX_GROUP_201xxxxx.csv. This way, we can merge all subject's files into one single file.

## Make plot

```r
#load data
library(ggplot2)
library(plyr) # this is required for using ddply
setwd("D:/Dropbox/R/Spine")
DTI_x=read.csv("meanROI_20160128.csv",header=FALSE,sep=",")
colnames(DTI_x)<-c('subject','group','scandate','metric','stattype','measurement','level')

#normal range
cdata<-ddply(subset(DTI_x, group=="control"),c("metric","level"),summarise,avg=mean(measurement),std=sd(measurement))

g1<-ggplot()+ geom_line(data=subset(DTI_x,metric=='fa'),aes(x=level,y=measurement,group=subject,color=group))+
  geom_point(data=subset(DTI_x,metric=='fa'),aes(x=level,y=measurement,group=subject,color=group),shape=1,size=3)

g1+xlab("Spine Level")+ylab("DTI Measurement")+
ggtitle('Evaluation of patient\'s throacic spine using DTI')+  
  theme(axis.title.y = element_text(size = rel(2))) +
  theme(axis.title.x = element_text(size = rel(2))) +
  theme(axis.text.x =  element_text(size = rel(1.5),angle=90,colour="black"))+
  theme(axis.text.y =  element_text(size = rel(1.5),colour="black"))+
  theme(strip.text.x = element_text(size = rel(1.5),colour="black"))+
  theme(plot.title = element_text(size=rel(2)))+
  theme(legend.position="top")+
  scale_x_discrete(limits=c("mid-C1","C1-C2","mid-C2","C2-C3","mid-C3",
                            "C3-C4","mid-C4","C4-C5","mid-C5","C5-C6",
                            "mid-C6","C6-C7","mid-C7","C7-T1","mid-T1",
                            "T1-T2","mid-T2","T2-T3","mid-T3","T3-T4",
                            "mid-T4","T4-T5","mid-T5","T5-T6","mid-T6",
                            "T6-T7","mid-T7","T7-T8","mid-T8","T8-T9",
                            "mid-T9", "T9-T10","mid-T10","T10-T11",
                            "mid-T11","T11-T12","mid-T12"))

#Plot them out
title=paste("DTI Metrics")
# plot normal first
scatterplot=ggplot(subset(DTI_x,stat=="mean"  ),aes(x=spine_level,y=value,color=ID,group=ID))
scatterplot=scatterplot+ggtitle(title)+geom_line()+geom_point(shape=1,size=3)+  
  scale_colour_manual(values=c("#C1CDCD","#7A8B8B","#2F4F4F","#FF0000"),labels=c("Control A","Control B","Control C","Patient"))+
    facet_wrap( ~ metric,  ncol=1,scales="free")
scatterplot



# Build the plot for abstract/manuscript
# Build this layer by layer
# Read data into R
DTI_x=read.csv("roi_mean_all_metrics_rows_nostd.csv",header=TRUE,sep=",")
# layer 1 : plot for all subjects
plot1=ggplot(subset(DTI_x,stat=="mean"),aes(x=level_name,y=value,color=ID,group=ID))+
  ggtitle(title)+geom_line()+geom_point(shape=1,size=3)+  
  scale_colour_manual(values=c("#C1CDCD","#7A8B8B","#2F4F4F","#FF0000"),labels=c("Control A","Control B","Control C","Patient"))+
  facet_wrap( ~ metric,  ncol=1,scales="free")+scale_x_discrete(limits=c("mid-T7","T7-T8","mid-T8","T8-T9","mid-T9","T9-T10","mid-T10","T10-T11"))+xlab("Spine Level")
plot1

plot1=ggplot(subset(DTI_x,stat=="mean" & Group=="Healthy"),aes(x=level_name,y=value,color=ID,group=ID))+
  ggtitle(title)+geom_line()+geom_point(shape=1,size=3)+  
  scale_colour_manual(values=c("#C1CDCD","#7A8B8B","#2F4F4F"),labels=c("Control A","Control B","Control C"))+
  facet_wrap( ~ metric,  ncol=1,scales="free")+scale_x_discrete(limits=c("mid-T7","T7-T8","mid-T8","T8-T9","mid-T9","T9-T10","mid-T10","T10-T11"))+xlab("Spine Level")+
  geom_smooth(method="lm")
plot1

plot2=ggplot(subset(DTI_x,stat=="mean" & Group=="Patient"),aes(x=level_name,y=value,color=ID,group=ID))+
  ggtitle(title)+geom_line()+geom_point(shape=1,size=3)+
  facet_wrap( ~ metric,  ncol=1,scales="free")+scale_x_discrete(limits=c("mid-T7","T7-T8","mid-T8","T8-T9","mid-T9","T9-T10","mid-T10","T10-T11"))+xlab("Spine Level")
plot2


plot2=ggplot(subset(DTI_x,stat=="mean" & Group=="Healthy" ))+
  geom_ribbon(aes(y=value,ymin=mean(value)-1.54*sd(value),ymax=mean(value)+1.54*sd(value)))+                                                                                                                          y
  facet_wrap( ~ metric,  ncol=1,scales="free")
plot2
###############################
### DO NOT CHANGE   
# composite drawing data
DTI_x=read.csv("roi_mean_all_metrics_rows_nostd.csv",header=TRUE,sep=",")
library(plyr)
cdata<-ddply(subset(DTI_x, Group=="Healthy"),c("metric","level_name"),summarise,avg=mean(value),std=sd(value))
plot1=ggplot()+geom_line(data=subset(DTI_x,stat=="mean" & Group=="Healthy"),aes(x=level_name,y=value,color=ID,group=ID))+
  geom_point(data=subset(DTI_x,stat=="mean" & Group=="Healthy"),aes(x=level_name,y=value,color=ID,group=ID),shape=1)+
  geom_line(data=subset(DTI_x,stat=="mean" & Group=="Patient"),aes(x=level_name,y=value,color=ID,group=ID))+
  geom_point(data=subset(DTI_x,stat=="mean" & Group=="Patient"),aes(x=level_name,y=value,color=ID,group=ID),shape=1)+
  geom_ribbon(data=cdata,aes(x=level_name,ymin=avg-1.64*std,ymax=avg+1.64*std,group=metric),alpha=0.2,fill="steelblue2",color=NA)+
  scale_x_discrete(limits=c("mid-T7","T7-T8","mid-T8","T8-T9","mid-T9","T9-T10","mid-T10","T10-T11"))+  
  scale_colour_manual(name="Subject",values=c("#00CCFF","#0000FF","#009966","#FF0000"),labels=c("Control A","Control B","Control C","Patient"))+  
  facet_wrap(~metric,,ncol=2,scales="free")+xlab("Spine Level")+ylab("DTI Measurement")

plot1+ggtitle('Evaluation of patient\'s throacic spine using DTI')+  
  theme(axis.title.y = element_text(size = rel(2))) +
  theme(axis.title.x = element_text(size = rel(2))) +
  theme(axis.text.x =  element_text(size = rel(1.5),angle=45,colour="black"))+
  theme(axis.text.y =  element_text(size = rel(1.5),colour="black"))+
  theme(strip.text.x = element_text(size = rel(1.5),colour="black"))+
  theme(plot.title = element_text(size=rel(2)))+
  theme(legend.position="top")

##################
#########################################


###
# 01/12/2016
###
### DO NOT CHANGE   
# composite drawing data
DTI_x=read.csv("roi_mean_all_metrics_rows_01127016.csv",header=TRUE,sep=",")
#add a group variable based on the first character in ID
library(ggplot2)
group=rep("NA",nrow(DTI_x))
for (i in 1:nrow(DTI_x))
{
  if (substr(DTI_x[i,1],0,1)=="A")
    group[i]="Healthy"
  else
    group[i]="Patient"
}
DTI_x$Group=as.factor(group)
# rename metric to a more expanded name
library(plyr)
DTI_x$metric=revalue(DTI_x$metric, c("fa"="Fractional Anisotropy", "AD"="Axial Diffusivity",
                                     "adc"="Apparent Diffusion Coefficient",
                                     "MD"="Mean Diffusivity",
                                     "RD"="Radial Diffusivity"))
#compute the average value for healthy group
cdata<-ddply(subset(DTI_x, Group=="Healthy" & stat=="mean"),c("metric","level"),summarise,avg=mean(value),std=sd(value))
plot1=ggplot()+geom_line(data=subset(DTI_x,stat=="mean" & Group=="Healthy"),aes(x=level,y=value,color=ID,group=ID))+
  geom_point(data=subset(DTI_x,stat=="mean" & Group=="Healthy"),aes(x=level,y=value,color=ID,group=ID),shape=1)+
  geom_line(data=subset(DTI_x,stat=="mean" & Group=="Patient"),aes(x=level,y=value,color=ID,group=ID))+
  geom_point(data=subset(DTI_x,stat=="mean" & Group=="Patient"),aes(x=level,y=value,color=ID,group=ID),shape=1)+
  geom_ribbon(data=cdata,aes(x=level,ymin=avg-1.64*std,ymax=avg+1.64*std,group=metric),alpha=0.2,fill="steelblue2",color=NA)+
  scale_x_discrete(limits=c("mid-T7","T7-T8","mid-T8","T8-T9","mid-T9","T9-T10","mid-T10","T10-T11"))+  
  scale_colour_manual(name="Subject",values=c("#00CCFF","#0000FF","#009966","#002266","#FF0000","#FFFF55"),labels=c("Control A","Control B","Control C","Control D","Patient-Past","Patient-Now"))+  
  facet_wrap(~metric,,ncol=2,scales="free")+xlab("Spine Level")+ylab("DTI Measurement")

plot1+ggtitle('Evaluation of a SCI patient\'s throacic spine using DTI')+  
  theme(axis.title.y = element_text(size = rel(2))) +
  theme(axis.title.x = element_text(size = rel(2))) +
  theme(axis.text.x =  element_text(size = rel(1.5),angle=45,colour="black"))+
  theme(axis.text.y =  element_text(size = rel(1.5),colour="black"))+
  theme(strip.text.x = element_text(size = rel(1.5),colour="black"))+
  theme(plot.title = element_text(size=rel(2)))+
  theme(legend.position="top")

##################






# plot mean of healthy
plot2=ggplot()+geom_line(data=cdata,aes(x=level_name,y=avg,group=metric))+facet_wrap(~metric,,scales="free",ncol=1)
plot2

# shaded
plot2=ggplot()+geom_ribbon(data=cdata,aes(x=level_name,ymin=avg-1.64*std,ymax=avg+1.64*std,group=metric),alpha=0.2,color="yellow",lty="dotted")+
  facet_wrap(~metric,,scales="free",ncol=1)
plot2


DTI_x=read.csv("roi_mean_all_metrics_withaverage.csv",header=TRUE,sep=",")
#Plot them out
title=paste("DTI Metrics")
# plot normal first
scatterplot=ggplot(subset(DTI_x,stat=="mean"  ),aes(x=level,y=value,color=ID,group=ID))
scatterplot=scatterplot+ggtitle(title)+geom_line()+geom_point(shape=1)+  
  scale_colour_manual(values=c("#C1CDCD","#7A8B8B","#2F4F4F","#FF0000"),labels=c("Control A","Control B","Control C","Patient"))+
  facet_wrap( ~ metric,  ncol=1,scales="free")
scatterplot
```
