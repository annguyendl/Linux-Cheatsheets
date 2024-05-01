![ImportedPhoto.735544807.206754](tar/img/ImportedPhoto.735544807.206754.jpeg)

# Create tar archives

| Description                                                  | Command                                                   |
| ------------------------------------------------------------ | --------------------------------------------------------- |
| Create a tar archive contains file1, file2 and dir1          | `tar -cvf my.tar file1 file2 dir1`                        |
| Create a gzip tar archive contains file1, file2 and dir1     | `tar -czvf my.tar.gz file1 file2 dir1`                    |
| Create a bzip tar archive contains file1, file2 and dir1     | `tar -cjvf my.tar.gz file1 file2 dir1`                    |
| Create a gzip tar archive contains file1, file2 and dir1 and specify the zip level | `tar cvf my.tar file1 file2 dir1 > gzip -9 - > my.tar.gz` |

# Extract tar archives

| Description                                 | Command                         |
| ------------------------------------------- | ------------------------------- |
| Extract a tar archive                       | `tar -xvf my.tar`               |
| Extract a tar archive to a target directory | `tar -xvf my.tar -C /tmp/mytar` |

# View tar contents

| Description                                               | Command           |
| --------------------------------------------------------- | ----------------- |
| List files in a tar archive. Work for compressed archives | `tar -tvf my.tar` |

# Update tar archives

| Description                    | Command                             |
| ------------------------------ | ----------------------------------- |
| Append files to an archive     | `tar -rvf my.tar newfile1 newfile2` |
| `Delete files from an archive` | `tar --delete -f my.tar file2`      |

# Password protect tar file

| Description                                                  | Command                                                  |
| ------------------------------------------------------------ | -------------------------------------------------------- |
| Create PGP password protected compressed tar archive. You will be prompted to enter a password | `tar -czvf - file1 file2 dir1 | gpg -c -o my.tar.gz.gpg` |
| Decrypt, uncompressed and extract archive                    | `gpg -d my.tar.gz.gpg | tar -xzvf -`                     |

