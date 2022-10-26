---
title: Restore individual files using single-file backup restore | Microsoft Docs
description: Restore an individual file using the single-file backup restore feature in Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-ahibbard
manager: ''
editor: ''

ms.assetid:
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.topic: how-to
ms.date: 10/26/2022
ms.author: anfdocs
---
# Restore individual files from backup vault using single-file backup restore

If you restore individual files no longer available in an online snapshot for [single-file snapshot restore](snapshots-restore-file-single.md), you can rely on your Azure NetApp Files backup vault to restore individual files. With single-file backup restore, you can restore a single file to a specific location in a volume or multiple files (up to 8) to a specific directory in the volume.
<!-- backup vault link pending -->

## Considerations

* If no destination path is provided during the restore operation, the given file(s) is restored in the original file location. If the file already exists, it will be overwritten.
    * If the file being restored has a multi-level directory depth (for example, `/dir1/dir2/file.txt`), all of the parent directories must be present in the active file system for the restore operation to succeed. The restore cannot create new directories. 
* If the destination path provided is invalid (non-existent in the Active file system), the operation will fail.
* A maximum of eight (8) files can be restored to a volume in a single operation. You must wait for a restore operation to complete to restore more files to that volume.
* The file list field has a character limit of 1024 characters. 
* The target volume for the restore operation must have enough logical free space available to accommodate all the files being restored.
* The restore operation will not work if a directory or a soft link path is entered in the file list field.

## Register the feature

The single file backup restore feature is currently in public preview. If you are using this feature for the first time, you need to register the feature first.

<!-- steps or contact? -->

## Steps

1. Under the **Backups** menu, select the backup you want to use to restore files.
1. Select the meatball menu for the backup you want to use, then select **Restore Files**.

    :::image type="content" source="../media/azure-netapp-files/restore-files-select.jpg" alt-text="The meatball menu under each backup has three options: restore to a new volume, delete, or restore files." lightbox="../media/azure-netapp-files/restore-files-select.jpg":::

1. In the **File paths** field, enter the file names with the complete file path for each file, for example `/dir/file1.txt`. Multiple files can be entered as a comma-separated list or with line breaks between entries.

    The menu includes three optional fields:
    * **NetApp account**: This field specifies the NetApp account to which the destination volume belongs. If no value is specified, the files will be restored to the original volume.

    * **Destination volume**: This field specifies the volume to which the files will be restored. If no value is specified, the files will be restored to the original volume.

    * **Destination path**: The field specifies the directory to which the files will be restored. The destination path must already exist in the destination volume.  If no value is specified, the files will be restored to the original volume.

    :::image type="content" source="../media/azure-netapp-files/restore-files-file-path.jpg" alt-text="The restore individual files from backup menu includes four fields: file paths, NetApp account, destination volume, and destination path." lightbox="../media/azure-netapp-files/restore-files-file-path.jpg":::

1. Select **Restore** to complete the operation. 


## Next steps

* [Understand Azure NetApp Files backup](backup-introduction.md)
* [Requirements and considerations for Azure NetApp Files](backup-requirements-considerations.md)
* [Resource limits for Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Configure policy-based backups](backup-configure-policy-based.md)
* [Configure manual backups](backup-configure-manual.md)
* [Manage backup policies](backup-manage-policies.md)
* [Search backups](backup-search.md)
* [Restore a backup to a new volume](backup-restore-new-volume.md)
* [Disable backup functionality for a volume](backup-disable.md)
* [Delete backups of a volume](backup-delete.md)
* [Volume backup metrics](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Azure NetApp Files backup FAQs](faq-backup.md)