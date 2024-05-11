# CS345



| **Date** | **Milestone**                                  | **Details**                                                  |
| -------- | ---------------------------------------------- | ------------------------------------------------------------ |
| 2/2/24   | Form  Group  Submit  paper prefs               | Find  like-minded students  Choose  at least 3 preferred papers to present |
| 12/2/24  | Draft  Proposal                                | Send your  2-page proposal by email                          |
| 19/2/24  | Finalize  Proposal  Checkpoint  #1 (10%)       | After  a back-and-forth  discussions with the instructor     |
| 25/3/24  | Midterm  Progress Report  Checkpoint  #2 (15%) | 4-page  report should read like parts of a research paper    |
| 27/3/24  | Midterm  Presentations                         | Define and motivate a problem, survey  related work, and form initial hypothesis and idea |
| 8/5/24   | Final  Presentation                            | Present  your findings in a presentation                     |
| 10/5/24  | Final  Report (25%)                            | 8-page final report similar to the  papers you read          |





•2 pages including references that *ideally* includes

•The particular results you would like to replicate

•Or the overall goal of your original project

•What is the problem? | Why is it important to solve?

•What you will do in some detail?| How would you evaluate your solution?

•A brief outline of incremental steps to do to finish the project as well as a timeline

•The goal is to convince both us and yourself that your project is neither too small nor too big

•Include group members, if any

•Schedule via email a 15-minute meeting to discuss





## 英语论文写作tip

1. 不要缩写, it is, does not 都不要缩写. 
2. 不要用using, 
3. 谷歌 synonym propose 来找一些关键词.
4. 解释每个术语.
5. 摘要不用缩写NN ,DL. 正文才要. 





## 画图经验

overleaf不太支持svg, 不要用png位图, 所以我 用pdf.

很多图的caption放latex就太小, 所以要大字号

应该可以把多个图拼起来的.  可以引用Fig. 9b. LaTeX 如何让两张图并排显示？ - PPluvcoder的回答 - 知乎  https://www.zhihu.com/question/41322252/answer/315983527

```
\begin{figure}
    \centering
    \subfigure[]{%
        \includegraphics[width=0.25\textwidth]{figures/speedup.pdf}
        \label{fig:train_whole_model}
    }\hfil
    \subfigure[]{%
        \includegraphics[width=0.25\textwidth]{figures/speedup.pdf}
        \label{fig:train_sl}
    }\hfil
    \caption{\subref{fig:train_whole_model}~...}
    \label{fig:pt_sl_comparison}
\end{figure}
```



应该用autoref吗?  我看了一些文章都是ref然后手动打 Fig.  envpipe甚至混合用.  目前理解是特别需要省文字的时候手动打Fig. 和 Eqn. 不需要的时候就autoref 就可以.  

bar可以用stack , 来画出多个因素. 

```latex
\begin{figure*} % \begin{figure*}就是整页. 
\centering
  \includegraphics[width=1\textwidth]{figures/throughput-1.pdf} 
  \caption{throughput vs l1 footprint}
  \label{fig:throughput}
  \includegraphics[width=1\textwidth]{figures/throughput-1.pdf} %一个begin figure可以放两个图
  \caption{throughput vs l1 footprint}
\end{figure*}
```



放在latex的  图的字体  要调整大一点.

可以看看AE 参考一下别人是怎么一键画图的, 不用excel画图. 别人的画图脚本.

图总是越画越好的, 很需要细心, 耐心,  

