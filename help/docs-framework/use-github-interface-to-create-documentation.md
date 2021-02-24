---
title: Use the GitHub web interface to create your new destination page
seo-title: Use the GitHub web interface to create your new destination page
description: The instructions on this page show you how to use the GitHub web interface to author documentation and submit a pull request.
seo-description: The instructions on this page show you how to use the GitHub web interface to author documentation and submit a pull request.
---

# Use the GitHub web interface to create your new destination page {#github-interface}

The instructions below show you how to use the GitHub web interface to author documentation and submit a pull request. Before going through the steps indicated here, make sure you read [Document your destination in Adobe Experience Platform Destinations](/help/docs-framework/documentation-instructions.md).

>[!TIP]
>
>Refer also to the supporting documentation in Adobe's contributor guide:
>* [Install Git and Markdown Authoring tools](https://docs.adobe.com/content/help/en/contributor/contributor-guide/setup/install-tools.html)
>* [Set up Git repository locally for documentation](https://docs.adobe.com/content/help/en/contributor/contributor-guide/setup/local-repo.html)
>* [GitHub contribution workflow for major changes](https://docs.adobe.com/content/help/en/contributor/contributor-guide/setup/full-workflow.html).

1. In your browser, navigate to `https://github.com/AdobeDocs/experience-platform.en`.
2. To [fork](https://docs.adobe.com/content/help/en/contributor/contributor-guide/setup/local-repo.html#fork-the-repository) the repository, click **Fork** as shown in the image below.

   ![Fork Adobe documentation repository](/help/docs-framework/assets/ssd-fork-repo.png)

3. In your fork of the repository, create a new branch for your project, as shown below. You will use this branch for the work in this tutorial.

   ![Create new GitHub branch](/help/docs-framework/assets/new-branch-github.gif)

4. In the GitHub folder structure of the forked repository, navigate to `experience-platform.en/help/destinations/catalog/[...]`, where `[...]` is the desired category for your destination. For example, if you are adding a personalization destination to Experience Platform, select the `personalization` category. Select **Add file > Create new file**. 

   >[!TIP]
   >
   >The folder structure in the screen recording below is outdated. Navigate to the folder structure indicated above.

   ![Add new file](/help/docs-framework/assets/github-navigate-and-create-file.gif)

5. Name your destination `YOURDESTINATION.md`, where YOURDESTINATION is the name of your destination in Adobe Experience Platform. For example, if your company is called Moviestar, you would name your file `moviestar.md`.
6. In your new file in GitHub, paste in the content of the [destination template](/help/docs-framework/self-service-template.md). Download the template [here](assets/yourdestination-template.md.zip). Unzip it to extract the `.md` file template.
7. In the GitHub interface, edit the template with relevant information for your destination. Follow the instructions in the template.
8. For any screenshots or images that you plan on using, use the GitHub interface to upload the files to `experience-platform.en/help/destinations/assets/catalog/` and link to them from the page you are authoring. See [instructions how to link to images](https://docs.adobe.com/content/help/en/contributor/contributor-guide/writing-essentials/linking.html#link-to-images).
   
   >[!TIP]
   >
   >The folder structure in the screen recording below is outdated. Navigate to the folder structure indicated above.

   ![Upload image to Github](/help/docs-framework/assets/upload-image.gif)

9.  When you are ready, save the file in your branch.

      ![Confirm file creation](/help/docs-framework/assets/ssd-confirm-file-creation.png)

10. After you saved the file and uploaded your desired images, you can open a pull request (PR) to merge your working branch into the master branch of the Adobe documentation repository. Make sure the branch that you worked on is selected and select **Pull request**.

   ![Create pull request](/help/docs-framework/assets/ssd-create-pull-request-1.png)

11. Make sure that the base and compare branches are correct. Add a note to the PR, describing your update, and select **Create pull request**. This opens a PR to merge the working branch of your fork into the master branch of the Adobe repository. 
   
   >[!TIP]
   >
   >Leave the **Allow edits by maintainers** checkbox selected so that the Adobe documentation team can make edits to the PR. 
   
   ![Create pull request to Adobe documentation repository](/help/docs-framework/assets/ssd-create-pull-request-2.png)

12. At this point, a notification appears that prompts you to sign the Adobe CLA. This is a mandatory step. After you signed it, refresh the PR page and submit the pull request.

13. You can confirm that the pull request has been submitted by inspecting the **Pull requests** tab in `https://github.com/AdobeDocs/experience-platform.en`.

   ![PR successful](/help/docs-framework/assets/ssd-pr-successful.png)

14. Thank you! The Adobe documentation team will reach out in the PR in case any edits are required and to let you know when the documentation will be published.

>[!TIP]
>
>To add images and links to your documentation, and for any other questions around Markdown, read [Using Markdown](https://docs.adobe.com/content/help/en/contributor/contributor-guide/writing-essentials/markdown.html) in Adobe's collaborative writing guide.