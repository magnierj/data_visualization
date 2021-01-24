# data_visualization

Each R script is accompanied with a pdf file with the same name.

## Clustering visualization 

Figure clustering with results from Admixture software (Alexander et al., 2009)
NB : This software is based on likelihood model and a block relaxation algorithm that alternates between updating the ancestry coefficient matrix Q and the population allele frequency matrix F.

The packages :
```
library(ggplot2)
library(tidyverse)
library(reshape)
library(gridExtra)
```

Import datasets : 
- samples_ID dataset with heading : ID_sample ; pop_sample 
- list of cattle populations 
```DET_IND=read.table("DATA.ind")[,c(-2)]
DET_POP=read.table("ORD_RACES_24pop",sep=" ")
Npops=dim(DET_POP)[1]
```
Clustering visualization from 2 cluster to 15 clusters
```graph <- list()

for (k in 2:15){
  DATA_Q=read.table(paste("DATA",k, "Q", sep="."))
  rownames(DATA_Q)=as.character(DET_IND[,1])
  
  #test geom_bar
  test_DATA_Q_ord<-rownames_to_column(DATA_Q,var="id")
  test_DATA_Q_ord<-melt(test_DATA_Q_ord, id=c("id"))
  
  ch<-str_split_fixed(test_DATA_Q_ord$id,"_",2)
  ch<-as.data.frame(ch)
  test_DATA_Q_ord<-cbind.data.frame(ch$V1,test_DATA_Q_ord)
  colnames(test_DATA_Q_ord)<-c("pop","id","classe", "val")
  test_DATA_Q_ord$pop<-factor(test_DATA_Q_ord$pop, levels = c("ANG", "AUB", "CHA", "HFD", "HOL", "JER","MON","SAL","BAO" ,"LAG" ,"NDA","SOM","BRM" ,  "GIR", "NEL",  "SGT","OUL","BOR" ,   "ZBO", "ZFU", "BAN","EAZ","ZMA","MAY"))
    
  graph[[k]]<-ggplotGrob(ggplot(data=test_DATA_Q_ord, aes(x= test_DATA_Q_ord$id, y= test_DATA_Q_ord$val, dodge=factor(test_DATA_Q_ord$classe))) +
                           geom_bar(aes(fill=factor(classe)),position = "fill", stat = "identity",width = 1)+
                           facet_grid(~pop, scales='free', space='free')+
                           #  scale_fill_gradientn(colours = rainbow(10))+
                           labs( y="Ancestry")+
                           ggtitle(paste("K=",k))+
                           theme(
                             legend.position = "none",
                             axis.title.x = element_blank(),
                             axis.text.x = element_blank(),
                             axis.ticks.x = element_blank(),
                             strip.background.x = element_rect(fill="white"),
                             strip.text.x = element_text(size=9, angle=90,face = 'bold'),
                             panel.spacing.x = unit(0.1,"lines")
                           ))
}
```
Visualization for 3 pages
```grid.arrange(grobs = graph[2:6], ncol=1)
grid.arrange(grobs = graph[7:11], ncol=1)
grid.arrange(grobs = graph[12:15], ncol=1)
```
