---
-api-id: T:Windows.Storage.ApplicationData
-api-type: winrt class
---

<!-- Class syntax.
public class ApplicationData : Windows.Storage.IApplicationData, Windows.Storage.IApplicationData2, Windows.Storage.IApplicationData3
-->

# Windows.Storage.ApplicationData

## -description
Provides access to the application data store. Application data consists of files and settings that are either local, roaming, or temporary.

## -remarks
### Types of application data

[ApplicationData](applicationdata.md) provides local, roaming, and temporary storage for app data on a per-user basis. Use this class to preserve app-specific data between sessions, users, and across multiple devices.

[ApplicationData](applicationdata.md) does not provide access to files in an app package. To do this, use [Windows.ApplicationModel.Package.InstalledLocation](../windows.applicationmodel/package_installedlocation.md).

[ApplicationData.Current](applicationdata_current.md) gives you the app's [ApplicationData](applicationdata.md) instance. Use this instance to get app folders or settings.

Folders are used to store app data as files on the file system. App settings are stored in key/value pairs that can be organized in to nested sets. Settings data is saved in the Windows registry.

These are the main types of app data: 
+ Local: stored on the device, backed up in the cloud, and persists across updates
+ LocalCache: persistent data that exists on the current device, not backed up, and persists across updates
+ SharedLocal: persistent across all app users
+ Roaming: exists on all devices where the user has installed the app
+ Temporary: can be deleted by the system at any time


### Using application folders

[LocalFolder](applicationdata_localfolder.md) persists across updates and gets backed up to the cloud as part of the device backup. Typically, this folder should be used for user data that would be lost if it were not backed up. Some examples of data stored in [LocalFolder](applicationdata_localfolder.md) are:
+ a user drawing for an art app
+ daily exercise history for a fitness app
+ a shopping list for a todo app
 By storing information in the [LocalFolder](applicationdata_localfolder.md), the user will not lose data after resetting the device or switching to a new device. For other types of local data that are easy to recreate and not necessary for backup and restore, use the [LocalCacheFolder](applicationdata_localcachefolder.md) or [TemporaryFolder](applicationdata_temporaryfolder.md).

[LocalCacheFolder](applicationdata_localcachefolder.md) and [TemporaryFolder](applicationdata_temporaryfolder.md) are both stored locally and are not backed up to the cloud. [LocalCacheFolder](applicationdata_localcachefolder.md) is under control of that app and is persistent across app sessions. [LocalCacheFolder](applicationdata_localcachefolder.md) should be used for generated content needed across app sessions, such as cached files, logs, or authentication tokens. [TemporaryFolder](applicationdata_temporaryfolder.md) is not guaranteed to be persistent across sessions and can be deleted by the system at any time.

[RoamingFolder](applicationdata_roamingfolder.md) is typically used for user preferences and customizations, links, and small data files. The contents of [RoamingFolder](applicationdata_roamingfolder.md) roam across the user's devices and app instances. [RoamingFolder](applicationdata_roamingfolder.md) should not be used for large amounts of data, data specific to a device, or data that relies on instant syncing.

Another folder, [SharedLocalFolder](applicationdata_sharedlocalfolder.md), is persistent across app user accounts and should be used for large files accessed by multiple users. There is some extra set up required to access [SharedLocalFolder](applicationdata_sharedlocalfolder.md). For more information on accessing and using this folder, see [SharedLocalFolder](applicationdata_sharedlocalfolder.md).

You can store your app data in app-specific, versioned formats. For more info, see [Version](applicationdata_version.md) and [SetVersionAsync](applicationdata_setversionasync.md).

For more details on using these APIs, see [Store and retrieve settings and other app data](http://msdn.microsoft.com/library/41676a02-325a-455e-8565-c9ec0bc3a8fe).

## -examples
The following code example demonstrates how to read or write to an [ApplicationData](applicationdata.md) folder of your choice. This example uses the [LocalFolder](applicationdata_localfolder.md), but the code can be slightly modified to access the [LocalCacheFolder](applicationdata_localcachefolder.md), [RoamingFolder](applicationdata_roamingfolder.md), [SharedLocalFolder](applicationdata_sharedlocalfolder.md), or [TemporaryFolder](applicationdata_temporaryfolder.md) based on how your data should be stored. [SharedLocalFolder](applicationdata_sharedlocalfolder.md) has some restrictions and needs special permissions to access, for more information, see [SharedLocalFolder](applicationdata_sharedlocalfolder.md).

```javascript

// This example code can be used to read or write to an ApplicationData folder of your choice.

var applicationData = Windows.Storage.ApplicationData.current;
var localFolder = applicationData.localFolder;  // Change this to var roamingFolder = applicationData.roamingFolder;
                                                // to use the RoamingFolder instead, for example.

// Write data to a file

function writeTimestamp() {
   localFolder.createFileAsync("dataFile.txt", Windows.Storage.CreationCollisionOption.replaceExisting)
      .then(function (sampleFile) {
         var formatter = new Windows.Globalization.DateTimeFormatting.DateTimeFormatter("longtime");
         var timestamp = formatter.format(new Date());

         return Windows.Storage.FileIO.writeTextAsync(sampleFile, timestamp);
      }).done(function () {      
      });
}

// Read data from a file

function readTimestamp() {
   localFolder.getFileAsync("dataFile.txt")
      .then(function (sampleFile) {
         return Windows.Storage.FileIO.readTextAsync(sampleFile);
      }).done(function (timestamp) {
         // Data is contained in timestamp
      }, function () {
         // Timestamp not found
      });
}
```

```csharp

// This example code can be used to read or write to an ApplicationData folder of your choice.

// Change this to Windows.Storage.StorageFolder roamingFolder = Windows.Storage.ApplicationData.Current.RoamingFolder
// to use the RoamingFolder instead, for example.
Windows.Storage.StorageFolder localFolder = Windows.Storage.ApplicationData.Current.LocalFolder;

// Write data to a file

async void WriteTimestamp()
{
   Windows.Globalization.DateTimeFormatting.DateTimeFormatter formatter = 
       new Windows.Globalization.DatetimeFormatting.DateTimeFormatter("longtime");

   StorageFile sampleFile = await localFolder.CreateFileAsync("dataFile.txt", 
       CreationCollisionOption.ReplaceExisting);
   await FileIO.WriteTextAsync(sampleFile, formatter.Format(DateTime.Now));
}

// Read data from a file

async Task ReadTimestamp()
{
   try
   {
      StorageFile sampleFile = await localFolder.GetFileAsync("dataFile.txt");
      String timestamp = await FileIO.ReadTextAsync(sampleFile);
      // Data is contained in timestamp
   }
   catch (Exception)
   {
      // Timestamp not found
   }
}
```

```vb

' This example code can be used to read or write to an ApplicationData folder of your choice.

' Change this to Dim roamingFolder As Windows.Storage.StorageFolder = Windows.Storage.ApplicationData.Current.RoamingFolder
' to use the RoamingFolder instead, for example.
Dim localFolder As Windows.Storage.StorageFolder = Windows.Storage.ApplicationData.Current.LocalFolder

' Write data to a file

Private Async Sub WriteTimestamp()
   Dim formatter As DateTimeFormatter = New DateTimeFormatter("longtime")

   Dim sampleFile As StorageFile = Await localFolder.CreateFileAsync("dataFile.txt", 
       CreationCollisionOption.ReplaceExisting)
   Await FileIO.WriteTextAsync(sampleFile, formatter.Format(DateTime.Now));
End Sub

' Read data from a file

Private Async Function ReadTimestamp() As Task
   Try
      Dim sampleFile As StorageFile = Await localFolder.GetFileAsync("dataFile.txt")
      Dim timestamp As string = Await FileIO.ReadTextAsync(sampleFile)
      ' Data is contained in timestamp
   Catch e1 As Exception
      ' Timestamp not found
   End Try
End Function
```

```cpp

// This example code can be used to read or write to an ApplicationData folder of your choice.

// Change this to StorageFolder^ roamingFolder = ApplicationData::Current->RoamingFolder; to 
// use the RoamingFolder instead, for example.
StorageFolder^ localFolder = ApplicationData::Current->LocalFolder;

// Write data to a file

void MainPage::WriteTimestamp()
{
   concurrency::task<StorageFile^> fileOperation = 
       localFolder->CreateFileAsync("dataFile.txt", CreationCollisionOption::ReplaceExisting);
   fileOperation.then([this](StorageFile^ sampleFile)
   {
      auto calendar = ref new Calendar;
      auto now = calendar->ToDateTime();
      auto formatter = ref new Windows::Globalization::DateTimeFormatting::DateTimeFormatter("longtime");

      return FileIO::WriteTextAsync(sampleFile, formatter->Format(now));
   }).then([this](task<void> previousOperation) {
      try {
         previousOperation.get();
      } catch (Platform::Exception^) {
         // Timestamp not written
      }
   });
}

// Read data from a file

void MainPage::ReadTimestamp()
{
   concurrency::task<StorageFile^> getFileOperation(localFolder->GetFileAsync("dataFile.txt"));
   getFileOperation.then([this](StorageFile^ file)
   {
      return FileIO::ReadTextAsync(file);
   }).then([this](concurrency::task<String^> previousOperation) {
      String^ timestamp;
 
      try {
         // Data is contained in timestamp
         timestamp = previousOperation.get();
      } catch (...) {
         // Timestamp not found
      }
   });
}
```



## -see-also
[Quickstart: Local application data (JavaScript)](http://msdn.microsoft.com/library/87dfe8e5-2d01-45cf-bcb1-25f54219a439), [Quickstart: Roaming application data (JavaScript)](http://msdn.microsoft.com/library/60f40214-c201-4afe-a2f5-0ef3a7de0076), [Quickstart: Temporary application data (JavaScript)](http://msdn.microsoft.com/library/2e875049-85ef-4581-b6df-f9f0663d93c9), [Store and retrieve settings and other app data](http://msdn.microsoft.com/library/41676a02-325a-455e-8565-c9ec0bc3a8fe), [Guidelines for roaming application data](https://msdn.microsoft.com/library/windows/apps/hh465094.aspx), [Guidelines for app settings](https://docs.microsoft.com/windows/uwp/app-settings/guidelines-for-app-settings), [ApplicationDataCompositeValue](applicationdatacompositevalue.md), [ApplicationDataContainer](applicationdatacontainer.md), [ApplicationDataContainerSettings](applicationdatacontainersettings.md), [Application settings sample (Windows 8.1, Windows Phone 8.1)](http://go.microsoft.com/fwlink/p/?linkid=226738), [Application data sample (Windows 8.1, Windows Phone 8.1)](http://go.microsoft.com/fwlink/p/?linkid=231478), [Application data sample (Windows 10)](http://go.microsoft.com/fwlink/p/?LinkId=620486)