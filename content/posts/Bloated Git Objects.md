---
date: 2024-12-31
layout: post
tags: 
title: bloated files in git
draft: "true"
---
### Why not store large files in git
- binaries
- logs
- configurations
- temporary files

📝 Large files can cause significant overhead during push and pull operations. When you check in these files, Git needs to store them in its repository, which can lead to slower performance and increased storage usage. This can be especially problematic for large teams or projects with many large files.

🤔 Consider a scenario where your project has hundreds of megabytes of binary data, configurations, logs, and other large files. When you check these files into Git, the repository needs to store them all, which can lead to slow cloning and pushing times for other team members. This can also cause issues with disk space usage, especially if you're working on a shared server or cloud environment.

💻 To avoid these problems, it's best to keep large files outside of Git altogether. Instead, use a version control system like Git to manage your code and other small files, while using a separate tool or service to manage and store your large files. This way, you can still benefit from version control without sacrificing performance or disk space usage.

💡 Some popular tools for managing large files include cloud storage services like Google Drive, Dropbox, or OneDrive, as well as specialized tools like Git Lens or Subversion. By using these tools in conjunction with Git, you can keep your large files organized and easily accessible while avoiding the overhead associated with checking them into Git.️️️️️It's not a good idea to check in huge files into git such as binaries, configurations, logs etc as there can be signification overhead while pushing and pulling those.️️


