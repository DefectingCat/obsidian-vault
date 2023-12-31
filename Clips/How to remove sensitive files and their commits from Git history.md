**Git** is a distributed version control system designed to handle everything from small to very large projects with speed and efficiency. Git allows multiple developers to work on the same project simultaneously, each with their own local copies of the project. These copies can later be synchronized with the main repository and with each other.

Most developers leave files with sensitive information in their projects and upload them on Git. This may leave their projects vulnerable to security breaches and their personal information can be compromised. If accidentally pushed, these files can be removed but will remain in the commit history. This poses a security risk since anyone with access to the repository can check out an old commit to retrieve the sensitive file. Therefore, to protect sensitive information, it's essential to remove both the file and its commit history from Git.

### Removing sensitive files and their commits from Git history

To remove sensitive files and their commit history in Git, follow these series of steps to rewrite and cleanse the repository's history.

1. **Backup your repository**: Before making any significant changes, it's good practice to create a backup of your repository. You can do this by simply cloning it to a different location on your machine or by making a zip archive.
    
2. **Use the** **`filter-branch`** **command:** To remove a specific file from the entire history, you can use the `filter-branch` command. Here's how:

```
git filter-branch --force --index-filter "git rm --cached --ignore-unmatch PATH-TO-YOUR-FILE" --prune-empty --tag-name-filter cat -- --all
```

Command to rewrite the history of the repository

> This command rewrites the entire history of the repository to remove references to the specified file. Here's a breakdown:
> 
> - `--force`: Ensures the command runs even if the repository seems to be already filtered.
>     
> - `--index-filter`: Rewrites the staging area (or `index`). In this case, it uses the `git rm` command to remove a specific file.
>     
>     - `--cached`: Tells `git rm` to untrack the file but also keep it in your working directory.
>         
>     - `--ignore-unmatch`: This ensures that the command doesn't fail if the file is absent in some commits.
>         
> - `PATH-TO-YOUR-FILE`: This placeholder should be replaced with the actual path to the file you want to remove.
>     
> - `--prune-empty`: Removes commits that become empty as a result (i.e., commits that only included changes related to the removed file).
>     
> - `--tag-name-filter cat`: Rewrites tags to point to the new commits resulting from the filtered branch. The `cat` command simply updates the tags.
>     
> - `-- --all`: Applies the filter to all refs in the repository, including branches and tags. The extra `--` separates the command from `git filter-branch` options`.`
>     

3. **Garbage collection**: After the above step, the commits with the sensitive files are disassociated but still present. To remove these old commits, run:
    

git for-each-ref --format="%(refname)" refs/original/ | xargs -I {} git update-ref -d {}

Command to list the references and then delete them

> This command lists all reference names (like branches and tags) under `refs/original/` and then deletes each of those references from the Git repository.

Next, run the garbage collector:

git gc --prune=now

git gc --aggressive --prune=now

Commands to prune the non-referenced objects and optimize the repository

> The first command immediately prunes objects not referenced by any commit, and the second aggressively optimizes the repository to further reduce its size after the sensitive data removal.

4. **Push the changes to the remote repository**: If you have pushed the sensitive file to a remote repository, you need to force push the changes to overwrite the history:
    

git push origin --force --all

Command to forcefully push branches to remote repository

> This command forcefully pushes all branches to the remote repository `origin`, overwriting its history to reflect the changes made locally, which includes the removal of the sensitive files from the commit history.

If you have tags, you'll also want to push them:

git push origin --force --tags

Command to forcefully push tags to remote repository

> This command forcefully pushes all tags to the remote repository "origin," ensuring that the tags' history on the remote aligns with the local modifications made, such as the removal of sensitive files from the commit history.

5. **Inform collaborators**: If others have cloned or fetched from the repository, inform them about the changes. They will need to re-clone the repository or try to rebase their local changes atop the modified history.
    

### Conclusion

When sensitive data accidentally enters a Git repository, it's imperative to remove not just the file but its entire commit history. By employing a series of given Git commands, one can cleanse the local repository of this data, and then forcefully push the corrected history to the remote repository, ensuring both branches and tags align with the sanitized history. This process protects the integrity of sensitive information while maintaining the repository's usability.