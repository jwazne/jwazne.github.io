---
layout: post
title: Xml files with Talend
date: 2018-02-07 22:00:00 +0100
description: How to read data from two joined input xml files and produce a xml file as output. # Add post description (optional)
img: talend_xml.png # Add image post (optional)
tags: [xml, talend]
---
## What we want to do
To join 2 input xml files to produce an output xml file

## What we'll need
Talend v6.4.1. installed (should work with previous version of Talend too)
Two xml files :
-	**authors.xml** containing a list of authors like this:

![authors.xml.png](https://cdn.filestackcontent.com/pcraQnAlRdmstaCxXq8W)

-	**books.xml** containing a list of books with authorId referencing an author in authors.xml file, like this:
![books.xml.png](https://cdn.filestackcontent.com/s625Eh8jRMGmFNurjkre)

## The results
We should get a new Xml file **books_with_authorname.xml** containing a list of book with author name, like this:
![job_design_run2.png](https://cdn.filestackcontent.com/VpvQbZkRkG4Haoi68Hdg)

It is the same file as **books.xml** plus the authorname field and minus the publish_date and description fields.

## Step 1 : Configure metadata
First things first.
Always configure the metadata before doing anything on Talend.

### Metadata for authors.xml file
Right click on **Metadata > File xml** and choose **Create file xml**
![xml_file_metadata.png](https://cdn.filestackcontent.com/PblV8XekTL68Rgho76Di)

Enter the file information as below and click **Next**:
![authors_xml_file_metadata_step1.png](https://cdn.filestackcontent.com/DidazPDvQLarij1pltJJ)

Mark the file as Input XML and click **Next**:
![authors_xml_file_metadata_step2.png](https://cdn.filestackcontent.com/9n4cmOaYTuyMonINrhDc)

Browse to the authors.xml file. If it is a valid Xml file, the structure of the file should appears.
Choose the right encoding, then click **Next**
![authors_xml_file_metadata_step3.png](https://cdn.filestackcontent.com/OEgJ4snjQFmGF8lZSQci)

Here's the step to tell Talend how it should read the Xml file:
![authors_xml_file_metadata_step4-1.png](https://cdn.filestackcontent.com/rPgLENOITuifRS42Mfuk)

Here we want to get all the authors information, so we need to loop on the **<author>** tag of the xml file and select the id attribute (marked as @id) and the **<authorName>** and **<authorAge>** tags.
  Here how it looks:
  ![authors_xml_file_metadata_step4-2.png](https://cdn.filestackcontent.com/6sTJbz0dTUa43xmiT7da)

Great ! Click on **Next** when you get the desired output.

> N.B. : At this stage we could export this file configuration into a context by clicking on **Export as context** button. But I will skip this part since I think it deserves a dedicated post to explain context environments.

Let's move on the final step to create our xml file metadata that we will use in all of our Talend jobs.
Here you can rename the fields and correct/change the data type of each fields.
> N.B. : Can be useful specially to increase size of String fields and to change date pattern of Date fields.

![authors_xml_file_metadata_step5.png](https://cdn.filestackcontent.com/lsKUqnVFSuu1bjnSwRIs)
For example in the picture above I've changed the size of authorName from 20 to 50 characters. Indeed Talend estimates the number of characters by using data sample, so it can under-estimate the maximum number of characters for a field.

When you're done, click the **Finish** button

You have now a new Xml file metadata named authors that you can use in all Xml related components in all jobs you want.

![authors_xml_file_metadata_done.png](https://cdn.filestackcontent.com/l1NjLSNnSZO2sifBNbFd)

### Metadata for books.xml file

For the books.xml file steps are the same.
Here is the configuration of the Step 4 for the books.xml file:
![books_xml_file_metadata_step4.png](https://cdn.filestackcontent.com/EZ5yxBAjReu1C6yOD5U8)

### Metadata for books_with_authors.xml file (output file)
Right click on **Metadata > File xml** and choose **Create file xml**
![xml_file_metadata.png](https://cdn.filestackcontent.com/PblV8XekTL68Rgho76Di)

Enter the file information as below and click **Next**:
![output_file_metadata_step1.png](https://cdn.filestackcontent.com/6dHqaiIaSTulWzRN9fnT)

Mark the file as **Output XML** and click **Next**:
![output_file_metadata_step2.png](https://cdn.filestackcontent.com/IVVFdWORFaTIgDFXbkAw)

Choose to create the file manually since it doesn't exist yet.
![output_file_metadata_step3.png](https://cdn.filestackcontent.com/BZTxrmeMTyKwcwS1nnax)
> N.B. : you could also create a result xml file and use it by choosing **Create from a file**

Click on **Schema Management** button and add fields to build the schema of the output file:
![output_file_metadata_step4-1.png](https://cdn.filestackcontent.com/wPQ1IwEnQbyf0Q7gywpM)

Change the name of the root node by "catalog" and right-click on the row and choose Add Sub-element:
![output_file_metadata_step4-2.png](https://cdn.filestackcontent.com/yDHVRbC2TsqjgkskHG1v)
Name the element **book** and click **Ok**
Right-click on the book element and choose Add Attribute named id.
Drag and drop the book_id field on the left to the column Related Column of the @id attribute.
![output_file_metadata_step4-3.png](https://cdn.filestackcontent.com/ie5dwm0jSOCPTLBxb0LQ)
This will map the book_id field to the book attribute in the output xml file.

Then create sub-element for each fields under the book element
![output_file_metadata_step4-4.png](https://cdn.filestackcontent.com/kbHilyaeTFC2KcIyysDg)

Finally set the book element as loop element
![output_file_metadata_step4-5.png](https://cdn.filestackcontent.com/1Of5BtFSSWmKLynFVys6)

Here's the summary of all the steps:
![output_file_metadata_summary.png](https://cdn.filestackcontent.com/4fOrzV2bTH4qYG4Yusps)

When done click **Next**
Review the name of the fields and data type and length and click **Finish**
![output_file_metadata_step5.png](https://cdn.filestackcontent.com/eckr3aO3SVORzWymXPdQ)

## Step 2 : Place components on the job
Create a job named **j_xml_output** and drag each of the metadata created in step 1 to the job canvas. For both authors and books choose tFileInputXML component when asked, for books_with_authors it won't ask since it is an output xml file metadata.
![component_to_choose.png](https://cdn.filestackcontent.com/hQ7igGaATUq205G4dMgV)
You should end up with the following:
![job_design_putting_metadata.png](https://cdn.filestackcontent.com/8e6NXdDqTlCe2lvvy05A)

## Step 3 : Joining input files
To join the two input xml files we'll use the tMap component. Find it on the Palette and add it to the design job between the two input files and the ouptut file:
![job_design_adding_tmap.png](https://cdn.filestackcontent.com/41MRd4UjReK9Cmj1XSEY)

Right click on the books component and choose **Row > Main**
![job_design_main_flow1.png](https://cdn.filestackcontent.com/SiJMO3ebTnWN9rusPROH)
A path should appears inviting you to link the book component with another one. Go to the tMap component and left-click.
![job_design_main_flow2.png](https://cdn.filestackcontent.com/3dYnZCZrTyWP4jz0AOz8)
This should link the book component to the tMap component so that the both components now are a sub-job.
![job_design_main_flow3.png](https://cdn.filestackcontent.com/sJu6DAQmRn67UaWfL36R)

Do the exact same link with the authors component. You should get the following:
![job_design_main_flow4.png](https://cdn.filestackcontent.com/MtLgNoXSHgGymydrppAA)
You can see on the screenshot above that the link between authors and tMap is dashed. This means the authors flow is a lookup.
The main flow remains the books list but we are going to look into authors file to get some information to complete the books list.

Double click on the tMap component. It should open the tMap editor.
Drag the authorId field of the row1 flow (books) to the Expr. key of the id field of the row2 flow (authors).

> N.B. : for more convenience rename rowX with a more meaningful name. For example here row1 should have been renamed to books and row2 to authors. The cool thing is that you can do it after linking fields. So you'll see the modification in the next screenshots.

So after having map the authorId of the books with the id of the authors, you should end up with this:
![job_design_tmap1.png](https://cdn.filestackcontent.com/Fp1Dp4bwQluoWyLIRSox)

And that's all for a basic join of two xml files. By clicking on the little magnifying glass with a red 1 in it you can set options of the join. Basically it is the same thing as **inner join** or **left join** in the SQL world.

## Step 4 : Inserting output and mapping fields
Now link the tMap component with the output xml file component
![job_design_tmap2.png](https://cdn.filestackcontent.com/YIrPNs5AQL3OqLglQaQY)

Double click on the tMap component to open the tMap editor and map the input fields with the output fields like this:
![job_design_tmap3.png](https://cdn.filestackcontent.com/bZGCpoUSayIEMq8yvbu5)

Validate your changes by clicking the **Ok** button
