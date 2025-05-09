# Latexdiff with subfiles (and how to debug it)

This blogpost is about how to generate a diff file for projects that consist of multiple `.tex` files (e.g., `paper.tex` and `intro.tex`).

Debugging latex is hard (thanks to its poorly structured output), not to mention debugging latexdiff. Even worse, there exists little information on how to debug files generated by latexdiff. Having struggled with latexdiff multiple times, I (finally) decided to write this blogpost and document how to generate a diff file using latexdiff (and various frequently encountered errors). 

References:
[Latexdiff with subfiles](https://tex.stackexchange.com/questions/167620/latexdiff-with-subfiles)



## Setup

Imagine you have a paper that consists of two `.tex` files. `paper.tex` and `intro.tex`. In `paper.tex`, you import `intro.tex`. To make things harder, you also defined a table in `intro.tex`. In addition, you have a bib file `reference.bib` as well.

Here's what the files look like:

`paper.tex`
```latex
% paper.tex
\documentclass[conference,compsoc]{IEEEtran}
\usepackage{booktabs}
\begin{document}


%-- don't print date
\date{}
\title{I am all about debugging latexdiff} 

\author{
}

\maketitle

\input{intro}  

\newpage
\bibliographystyle{plain}
\bibliography{references}


\end{document}
```


`intro.tex`
```latex
% intro.tex
\section{Introduction}
% content credits to ChatGPT
In the realm of academic publishing, version control and document comparison are essential tasks for researchers, particularly when revisions are requested during peer review. As documents evolve through various stages of drafting, feedback incorporation, and refinement, a reliable way to track and highlight changes between versions is crucial. 

LaTeX itself is the standard typesetting system for academic documents, offering precise control over formatting, references, and citations, making it especially popular in disciplines such as mathematics, computer science, and physics. As LaTeX projects grow in complexity, it is common for researchers to manage multiple revisions over time, leading to the need for tools like latexdiff to efficiently manage document comparisons. 

The utility of latexdiff lies in its ability to generate a marked-up version of a LaTeX file, where changes such as insertions, deletions, and modifications are highlighted. While this functionality is invaluable, it is often hindered by several common issues: broken commands, improper formatting, and the handling of non-standard LaTeX packages. 

In this paper, we aim to provide a comprehensive guide for debugging latexdiff issues, focusing on practical techniques to troubleshoot common problems. We will explore strategies such as modifying LaTeX code to accommodate latexdiff, employing alternative comparison tools when necessary, and developing custom patches to address specific challenges. 

Through a series of case studies and practical examples, we will demonstrate how users can effectively debug latexdiff to streamline the revision process. By understanding the limitations of the tool and developing solutions to common pitfalls, researchers can enhance their productivity and reduce the time spent on document revisions. Ultimately, this paper aims to empower users to fully leverage latexdiff as a tool for managing document versions while minimizing the headaches associated with debugging its output.

Thanks to ChatGPT for generating this intro~\ref{ChatGPTW42:online}.

\begin{table}[h!]
\centering
\begin{tabular}{|c|c|c|}
\hline
\textbf{Column 1} & \textbf{Column 2} & \textbf{Column 3} \\ \hline
Row 1, Col 1      & Row 1, Col 2      & Row 1, Col 3      \\ \hline
Row 2, Col 1      & Row 2, Col 2      & Row 2, Col 3      \\ \hline
Row 3, Col 1      & Row 3, Col 2      & Row 3, Col 3      \\ \hline
\end{tabular}
\caption{A simple table example.}
\label{tab:simple_table}
\end{table}
```


`reference.bib`
```latex
% reference.bib
\documentclass[conference,compsoc]{IEEEtran}
\usepackage{booktabs}
\begin{document}


%-- don't print date
\date{}
\title{I am all about debugging latexdiff} 

\author{
}

\maketitle

\input{intro}  

\newpage
\bibliographystyle{plain}
\bibliography{references}


\end{document}
```


## Making Changes.
Later, you made a bunch of changes to these files (e.g., because you were asked to by your shepherd.). For simplicity, lets say you removed the first paragraph in intro, and you change the first cell in the table. You also updated the author list. Now the files look like:

`paper.tex`
```latex
% paper.tex
\documentclass[conference,compsoc]{IEEEtran}
\usepackage{booktabs}
\begin{document}


%-- don't print date
\date{}
\title{I am all about debugging latexdiff} 

\author{
Enze Liu % added name
}

\maketitle

\input{intro}  

\newpage
\bibliographystyle{plain}
\bibliography{references}


\end{document}
```


`intro.tex`
```latex
% intro.tex
\section{Introduction}
% content credits to ChatGPT
%% Removed first paragraph %%
% In the realm of academic publishing, version control and document comparison are essential tasks for researchers, particularly when revisions are requested during peer review. As documents evolve through various stages of drafting, feedback incorporation, and refinement, a reliable way to track and highlight changes between versions is crucial. 

LaTeX itself is the standard typesetting system for academic documents, offering precise control over formatting, references, and citations, making it especially popular in disciplines such as mathematics, computer science, and physics. As LaTeX projects grow in complexity, it is common for researchers to manage multiple revisions over time, leading to the need for tools like latexdiff to efficiently manage document comparisons. 

The utility of latexdiff lies in its ability to generate a marked-up version of a LaTeX file, where changes such as insertions, deletions, and modifications are highlighted. While this functionality is invaluable, it is often hindered by several common issues: broken commands, improper formatting, and the handling of non-standard LaTeX packages. 

In this paper, we aim to provide a comprehensive guide for debugging latexdiff issues, focusing on practical techniques to troubleshoot common problems. We will explore strategies such as modifying LaTeX code to accommodate latexdiff, employing alternative comparison tools when necessary, and developing custom patches to address specific challenges. 

Through a series of case studies and practical examples, we will demonstrate how users can effectively debug latexdiff to streamline the revision process. By understanding the limitations of the tool and developing solutions to common pitfalls, researchers can enhance their productivity and reduce the time spent on document revisions. Ultimately, this paper aims to empower users to fully leverage latexdiff as a tool for managing document versions while minimizing the headaches associated with debugging its output.

Thanks to ChatGPT for generating this intro~\ref{ChatGPTW42:online}.

\begin{table}[h!]
\centering
\begin{tabular}{|c|c|c|}
\hline
%% Change the first cell %%
\textbf{Column 1} & \textbf{Column 2} & \textbf{Column 3} \\ \hline
Row -1, Col -1      & Row 1, Col 2      & Row 1, Col 3      \\ \hline
Row 2, Col 1      & Row 2, Col 2      & Row 2, Col 3      \\ \hline
Row 3, Col 1      & Row 3, Col 2      & Row 3, Col 3      \\ \hline
\end{tabular}
\caption{A simple table example.}
\label{tab:simple_table}
\end{table}
```


## Generate the diff.tex File
To generate a diff.tex, you do the following:

```bash
#!/bin/bash
# generate diff files on Mac

## Define a few variables ##
## Full path to the directory that contain the old version of the paper
old_path="/abs/path/to/version_old" # e.g., /usr/bin/paper_v1/
## Full path to the directory that contain the new version of the paper
new_path="/abs/path/to/version_new" # e.g., /usr/bin/paper_v2/
## Main file (e.g., main.tex / paper.tex)
doc_name="paper.tex"
############################


## Coalesce all content into one file ####
cd "$old_path"
latexdiff --flatten "$doc_name" "$doc_name" > flat.tex

cd "$new_path"
latexdiff --flatten "$doc_name" "$doc_name" > flat.tex
##########################################



########### Generate diff ################
cd "$new_path"
latexdiff "$old_path/flat.tex" "$new_path/flat.tex" > "$new_path/diff.tex"
##########################################
```



## Generated diff.tex ##

Your `diff.tex` file should look like this:
```text
\documentclass[conference,compsoc]{IEEEtran} %DIF > 
%DIF -------
\usepackage{booktabs}
%DIF PREAMBLE EXTENSION ADDED BY LATEXDIFF
%DIF UNDERLINE PREAMBLE %DIF PREAMBLE
\RequirePackage[normalem]{ulem} %DIF PREAMBLE
\RequirePackage{color}\definecolor{RED}{rgb}{1,0,0}\definecolor{BLUE}{rgb}{0,0,1} %DIF PREAMBLE
.....
\providecommand{\DIFdelbeginFL}{} %DIF PREAMBLE
\providecommand{\DIFdelendFL}{} %DIF PREAMBLE
%DIF END PREAMBLE EXTENSION ADDED BY LATEXDIFF

\begin{document}


%-- don't print date
\date{}
\title{I am all about debugging latexdiff} 

\author{
\DIFaddbegin \DIFadd{Enze Liu
}\DIFaddend }

\maketitle

%DIF >  intro.tex
\section{Introduction}
% content credits to ChatGPT
\DIFdelbegin \DIFdel{In the realm of academic publishing, version control and document comparison are essential tasks for researchers, particularly when revisions are requested during peer review. As documents evolve through various stages of drafting, feedback incorporation, and refinement, a reliable way to track and highlight changes between versions is crucial. 
}\DIFdelend %DIF > % Removed first paragraph %%
%DIF >  In the realm of academic publishing, version control and document comparison are essential tasks for researchers, particularly when revisions are requested during peer review. As documents evolve through various stages of drafting, feedback incorporation, and refinement, a reliable way to track and highlight changes between versions is crucial. 

LaTeX itself is the standard typesetting system for academic documents, offering precise control over formatting, references, and citations, making it especially popular in disciplines such as mathematics, computer science, and physics. As LaTeX projects grow in complexity, it is common for researchers to manage multiple revisions over time, leading to the need for tools like latexdiff to efficiently manage document comparisons. 

The utility of latexdiff lies in its ability to generate a marked-up version of a LaTeX file, where changes such as insertions, deletions, and modifications are highlighted. While this functionality is invaluable, it is often hindered by several common issues: broken commands, improper formatting, and the handling of non-standard LaTeX packages. 

In this paper, we aim to provide a comprehensive guide for debugging latexdiff issues, focusing on practical techniques to troubleshoot common problems. We will explore strategies such as modifying LaTeX code to accommodate latexdiff, employing alternative comparison tools when necessary, and developing custom patches to address specific challenges. 

Through a series of case studies and practical examples, we will demonstrate how users can effectively debug latexdiff to streamline the revision process. By understanding the limitations of the tool and developing solutions to common pitfalls, researchers can enhance their productivity and reduce the time spent on document revisions. Ultimately, this paper aims to empower users to fully leverage latexdiff as a tool for managing document versions while minimizing the headaches associated with debugging its output.

Thanks to ChatGPT for generating this intro~\ref{ChatGPTW42:online}.

\begin{table}[h!]
\centering
\begin{tabular}{|c|c|c|}
\hline
%DIF > % Change the first cell %%
\textbf{Column 1} & \textbf{Column 2} & \textbf{Column 3} \\ \hline
Row \DIFdelbeginFL \DIFdelFL{1, Col 1      }\DIFdelendFL \DIFaddbeginFL \DIFaddFL{-1, Col -1      }\DIFaddendFL & Row 1, Col 2      & Row 1, Col 3      \\ \hline
Row 2, Col 1      & Row 2, Col 2      & Row 2, Col 3      \\ \hline
Row 3, Col 1      & Row 3, Col 2      & Row 3, Col 3      \\ \hline
\end{tabular}
\caption{A simple table example.}
\label{tab:simple_table}
\end{table}  

\newpage
\bibliographystyle{plain}
\bibliography{references}


\end{document}
```

## Generate diff.pdf and Debug
Now, upload everything in the `version_new` directory to **Overleaf**, which helps you generate the diff.pdf file. 

One thing I always delete is the duplicate DIF PREAMBLE EXTENSION ADDED BY LATEXDIFF (which for some reason exists twice), as demonstrated below.

```latex
\documentclass[conference,compsoc]{IEEEtran} %DIF > 
%DIF -------
\usepackage{booktabs}

%%% Appearing once %%%
%DIF PREAMBLE EXTENSION ADDED BY LATEXDIFF
%DIF UNDERLINE PREAMBLE %DIF PREAMBLE
\RequirePackage[normalem]{ulem} %DIF PREAMBLE
\RequirePackage{color}\definecolor{RED}{rgb}{1,0,0}\definecolor{BLUE}{rgb}{0,0,1} %DIF PREAMBLE
.....
\providecommand{\DIFdelbeginFL}{} %DIF PREAMBLE
\providecommand{\DIFdelendFL}{} %DIF PREAMBLE
%DIF END PREAMBLE EXTENSION ADDED BY LATEXDIFF

%%% Appearing twice %%%
%%% This is a duplicate of whats above %%%
%%% I alawys delete the second one %%%
%DIF PREAMBLE EXTENSION ADDED BY LATEXDIFF
%DIF UNDERLINE PREAMBLE %DIF PREAMBLE
\RequirePackage[normalem]{ulem} %DIF PREAMBLE
\RequirePackage{color}\definecolor{RED}{rgb}{1,0,0}\definecolor{BLUE}{rgb}{0,0,1} %DIF PREAMBLE
.....
\providecommand{\DIFdelbeginFL}{} %DIF PREAMBLE
\providecommand{\DIFdelendFL}{} %DIF PREAMBLE
%DIF END PREAMBLE EXTENSION ADDED BY LATEXDIFF
```
There's other issues you might encounter. I hope you find Overleaf's debug output helpful. Things I've tried include keeping the text in `paper.tex` unchanged (latexdiff often messes with package import and command definitions), ignoring changes in bib and appendix (i.e., remove all highlights in bib / appendix), and/or removing all changes in a table. 
 