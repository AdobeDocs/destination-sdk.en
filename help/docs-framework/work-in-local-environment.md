---
title: Use a text editor in your local environment to create your new destination page
seo-title: Use a text editor in your local environment to create your new destination page
description: The instructions on this page show you how to use a text editor to work in your local environment to author documentation and submit a pull request.
seo-description: The instructions on this page show you how to use a text editor to work in your local environment to author documentation and submit a pull request.
---

# Use a text editor in your local environment to create your new destination page {#local-authoring}

The instructions on this page show you how to use a text editor to work in your local environment to author documentation and submit a pull request.

>[!TIP]
>
>Refer also to our supporting documentation in Adobe's contributor guide:
>* [Install Git and Markdown Authoring tools](https://docs.adobe.com/content/help/en/contributor/contributor-guide/setup/install-tools.html)
>* [Set up Git repository locally for documentation](https://docs.adobe.com/content/help/en/contributor/contributor-guide/setup/local-repo.html)
>* [GitHub contribution workflow for major changes](https://docs.adobe.com/content/help/en/contributor/contributor-guide/setup/full-workflow.html).

1. In your browser, navigate to `https://github.com/AdobeDocs/experience-platform.en`
1. To [fork](https://docs.adobe.com/content/help/en/contributor/contributor-guide/setup/local-repo.html#fork-the-repository) the repository, click **Fork** as shown in the screenshot.

   ![Fork Adobe documentation repository](/help/docs-framework/assets/ssd-fork-repo.png)

2. Clone the repository to your local machine. Select **Code > HTTPS > Open with GitHub Desktop**, as shown below. Make sure you have [GitHub Desktop](https://desktop.github.com/) installed. For further reference, read [Create a local clone of the repository](https://docs.adobe.com/content/help/en/contributor/contributor-guide/setup/local-repo.html#create-a-local-clone-of-the-repository) in the Adobe contributor guide.

   ![Clone Adobe documentation repository to local environment](/help/docs-framework/assets/clone-local.png)

3. In your local file structure, navigate to `experience-platform.en/help/destinations/catalog/[...]`, where `[...]` is the desired category for your destination. For example, if you are adding a personalization destination to Experience Platform, select the `personalization` folder.
4. You will base your documentation on the [self-service destination template](/help/docs-framework/self-service-template.md). Download the [destination template](assets/yourdestination-template.md.zip). Unzip it and extract the file `yourdestination-template.md` to the above directory.  Rename the file `YOURDESTINATION.md`, where YOURDESTINATION is the name of your destination in Adobe Experience Platform. For example, if your company is called Moviestar, you would name your file `moviestar.md`.
5. Open your new file in your [text editor of choice](https://docs.adobe.com/content/help/en/contributor/contributor-guide/setup/install-tools.html#understand-markdown-editors).
6. Edit the template with relevant information for your destination. Follow the instructions in the template. 
7.  For any screenshots or images that you plan on adding to your documentation, navigate to `GitHub/experience-platform.en/help/destinations/assets/catalog/[...]`, where `[...]` is the desired category for your destination. For example, if you are adding a personalization destination to Experience Platform, select the `personalization` folder. Create a new folder for your destination and drop your images here. You can link to them from the page you are authoring. See [instructions how to link to images](https://docs.adobe.com/content/help/en/contributor/contributor-guide/writing-essentials/linking.html#link-to-images).
8. When you are ready, save the file you are working on.
9. In GitHub Desktop, create a working branch for your updates and select **Publish branch** to publish the branch to GitHub.

   >[!TIP]
   >
   >The folder structure in the screen recording below is outdated. For a personalization destination named Moviestar, you would use the following folder structure:
   >* `help/destinations/catalog/personalization/moviestar.md` for the Markdown file.
   >* `help/destinations/assets/catalog/personalization/moviestar/` for any images you are using in the documentation.

   ![new branch local](/help/docs-framework/assets/new-branch-local.gif)

10. In GitHub Desktop, [commit](https://docs.github.com/en/free-pro-team@latest/github/getting-started-with-github/github-glossary#commit) your work, as shown below.

   >[!TIP]
   >
   >The folder structure in the screen recording below is outdated. For a personalization destination named Moviestar, you would use the following folder structure:
   >* `help/destinations/catalog/personalization/moviestar.md` for the Markdown file.
   >* `help/destinations/assets/catalog/personalization/moviestar/` for any images you are using in the documentation.

   ![commit local](/help/docs-framework/assets/commit-local.png)
11. In GitHub Desktop, [push](https://docs.github.com/en/free-pro-team@latest/github/getting-started-with-github/github-glossary#push) your work to the [remote](https://docs.github.com/en/free-pro-team@latest/github/getting-started-with-github/github-glossary#remote) branch, as shown below.

   ![push your commit](/help/docs-framework/assets/push-local-to-remote.png)

12. In the GitHub web interface, open a pull request (PR) to merge your working branch into the master branch of the Adobe documentation repository. Make sure the branch you worked on is selected and select **Pull request**.

   ![create pull request](/help/docs-framework/assets/ssd-create-pull-request-1.png)

13. Make sure that the base and compare branches are correct. Add a note to the PR, describing your update, and select **Create pull request**. This opens a PR to merge the working branch of your fork into the master branch of the Adobe repository. 
   >[!TIP]
   >
   >Leave the **Allow edits by maintainers** checkbox selected so that the Adobe documentation team can make edits to the PR. 
 
   ![create pull request to adobe repo](/help/docs-framework/assets/ssd-create-pull-request-2.png)

14. At this point, a notification appears that prompts you to sign the Adobe CLA. Note that this is a mandatory step. After you signed it, refresh the PR page and merge the pull request.
15.  You can confirm that the pull request has been submitted by inspecting the **Pull requests** tab in `https://github.com/AdobeDocs/experience-platform.en`.

   ![PR successful](/help/docs-framework/assets/ssd-pr-successful.png)

16. Thank you! The Adobe documentation team will reach out in the PR in case any edits are required and to let you know when the documentation will be published.

>[!TIP]
>
>To add images and links to your documentation, and for any other questions around Markdown, read [Using Markdown](https://docs.adobe.com/content/help/en/contributor/contributor-guide/writing-essentials/markdown.html) in Adobe's collaborative writing guide.