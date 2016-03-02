layout: post
title: PortalException in moveFolders method will cause inconsistency issue between database and file system
date: 2015-12-18 19:35:54
category: 
- Liferay
tags:
- Liferay
- document & library
---

When we try to move folders from one repository to another one, if the source file is deleted in file system, the move process would be roll back due to transaction, while files before exception would be moved to new repository. This causes inconsistency.

 

The detail https://issues.liferay.com/browse/LPS-61337

It depends on https://issues.liferay.com/browse/LPS-60884

 

Here is what I do:

I delete a file in file system and try to move it to another folder. Then I debug that in FileSystemStore.getFileAsStream(companyId, repositoryId, fileName, versionLabel)

```

public InputStream getFileAsStream(
      long companyId, long repositoryId, String fileName,
      String versionLabel)
   throws PortalException {

   if (Validator.isNull(versionLabel)) {
      versionLabel = getHeadVersionLabel(
         companyId, repositoryId, fileName);
   }

   File fileNameVersionFile = getFileNameVersionFile(
      companyId, repositoryId, fileName, versionLabel);

   try {
      return new FileInputStream(fileNameVersionFile);
   }
   catch (FileNotFoundException fnfe) {
      throw new NoSuchFileException(fileNameVersionFile.getPath(), fnfe);
   }
}
```


It throws FileNotFoundException. But the moving file action processed successfully.

I continue debug about the exception, portal goes to DLFileEntryIndexer.doGetDocument(obj) // It's about update index
```

protected Document doGetDocument(Object obj) throws Exception {
   DLFileEntry dlFileEntry = (DLFileEntry)obj;

   if (_log.isDebugEnabled()) {
      _log.debug("Indexing document " + dlFileEntry);
   }

   boolean indexContent = true;

   InputStream is = null;

   try {
      String[] ignoreExtensions = PrefsPropsUtil.getStringArray(
         PropsKeys.DL_FILE_INDEXING_IGNORE_EXTENSIONS, StringPool.COMMA);

      if (ArrayUtil.contains(
            ignoreExtensions,
            StringPool.PERIOD + dlFileEntry.getExtension())) {

         indexContent = false;
      }

      if (indexContent) {
         is = dlFileEntry.getFileVersion().getContentStream(false);
      }
   }
   catch (Exception e) {
   }
   
   DLFileVersion dlFileVersion = dlFileEntry.getFileVersion();

```

You can see that it catch the exception, but does nothing. So the code continue doing the rest process.

I come up with an idea about moveFolder, we can doing the same thing when the file is lost. But you know, we just hide the exception to make the process continue. Because in all, liferay doesn't support transaction for file system.

 

Here is my solution https://github.com/daledotshan/liferay-portal/pull/367

In my first commit, I catch the exception and print it out. I don't throw exception in order to continue the moving process. In this way, we can avoid the inconsistency thing.

In my second commit, I don't delete the source folder if the source folder is not empty. Because if the files are all good, they will be moved to new folder. So the source folder would be empty and it can be deleted. So the left files are not found in file system.