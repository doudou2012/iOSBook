### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-03-03 | Alfred Jiang | - |

### 方案名称
CoreData - 使用 FYHDBManager 管理 CoreData

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
CoreData \ FYHDBManager

### 需求场景
1. 部分轻型小应用的 Coredata 管理需求

### 参考链接
（无）

### 详细内容
#####定义
FYHDBHeader.h

    //
    //  FYHDBHeader.h
    //  GrandJustice
    //
    //  Created by Alfred Jiang on 3/3/15.
    //  Copyright (c) 2015 FYH. All rights reserved.
    //

    #ifndef GrandJustice_FYHDBHeader_h
    #define GrandJustice_FYHDBHeader_h

    #define NAME_OF_SQLITE  @"GrandJustice.sqlite"
    #define NAME_OF_MODELD  @"GrandJustice"

    //根据实际需要增加实体定义
    #define ENTITY_RESULT_ITEM_NAME     @"GJResultItem"
    #define ENTITY_GJPLAYER_NAME        @"GJPlayer"

    #endif

FYHDBManager.h

    //
    //  FYHDBManager.h
    //  ALFNote
    //
    //  Created by FYH on 7/22/14.
    //  Copyright (c) 2014 FYH. All rights reserved.
    //

    #import <Foundation/Foundation.h>
    #import <CoreData/CoreData.h>
    #import "FYHDBHeader.h"

    typedef void (^FYHDBOperationCompletionBlock) (NSInteger type, NSError *error);

    typedef NS_ENUM(NSInteger, ResultType) {
        ResultType_Success = 1,
        ResultType_Not_Exist = -1000,
        ResultType_Save_Failed,
        ResultType_Delete_Failed,
        ResultType_Clear_Failed,
        ResultType_Fetch_Failed,
    };

    typedef NS_ENUM(NSInteger, OperationType) {
        OperationType_Save = 0,
        OperationType_Delete,
    };

    @interface FYHDBManager : NSObject

    - (id)init;
    - (NSManagedObjectContext *)mainManagedObjectContext;
    - (NSManagedObjectModel *)managedObjectModel;
    - (NSPersistentStoreCoordinator *)persistentStoreCoordinator;
    - (NSURL *)applicationDocumentsDirectory;
    - (void)saveManagedObject:(NSManagedObject *)object completion:(FYHDBOperationCompletionBlock)completionBlock;
    - (void)deleteDBObject:(id)object completion:(FYHDBOperationCompletionBlock)completionBlock;
    - (NSArray *)fetchDataArrayForEntity:(NSString *)entityName
                            byPredicates:(NSPredicate *)predicate
                         sortDescriptors:(NSArray *)sortDescriptiors
                  inManagedObjectContext:(NSManagedObjectContext *)context;

    @end

FYHDBManager.m

    //
    //  FYHDBManager.m
    //  ALFNote
    //
    //  Created by FYH on 7/22/14.
    //  Copyright (c) 2014 FYH. All rights reserved.
    //

    #import "FYHDBManager.h"
    #import <CoreData/CoreData.h>

    @interface FYHDBManager()
    {
        NSManagedObjectContext * __mainManagedObjectContext;
        NSManagedObjectModel * __managedObjectModel;
        NSPersistentStoreCoordinator * __persistentStoreCoordinator;
    }

    @end

    @implementation FYHDBManager

    - (id)init
    {
        self = [super init];

        __mainManagedObjectContext = [self mainManagedObjectContext];

        NSNotificationCenter *nc = [NSNotificationCenter defaultCenter];
        [nc addObserver:self selector:@selector(mergeChanges:) name:NSManagedObjectContextDidSaveNotification object:nil];

        return self;
    }

    #pragma mark - CoreData method

    - (void)mergeChanges:(NSNotification *)notification {
        NSManagedObjectContext *savedContext = [notification object];

        // ignore change notifications for the main MOC
        if (__mainManagedObjectContext == savedContext)
        {
            return;
        }

        dispatch_sync(dispatch_get_main_queue(), ^{
            [__mainManagedObjectContext mergeChangesFromContextDidSaveNotification:notification];
        });
        //this tells the main thread moc to run on the main thread, and merge in the changes there
        //[__mainManagedObjectContext performSelectorOnMainThread:@selector(mergeChangesFromContextDidSaveNotification:) withObject:notification waitUntilDone:YES];
    }

    - (void)saveContext
    {
        NSError *error = nil;
        NSManagedObjectContext *managedObjectContext = self.mainManagedObjectContext;
        if (managedObjectContext != nil) {
            if ([managedObjectContext hasChanges] && ![managedObjectContext save:&error]) {
                // Replace this implementation with code to handle the error appropriately.
                // abort() causes the application to generate a crash log and terminate. You should not use this function in a shipping application, although it may be useful during development.
                NSLog(@"Unresolved error %@, %@", error, [error userInfo]);
                abort();
            }
        }
    }

    #pragma mark - Core Data stack

    -(void)saveManagedObject:(NSManagedObject *)object completion:(FYHDBOperationCompletionBlock)completionBlock
    {
        NSManagedObjectContext *moc = object.managedObjectContext;

        if (moc == __mainManagedObjectContext) {
            [moc performBlockAndWait:^{
                NSError *error;
                [moc save:&error];
                completionBlock(OperationType_Save, error);
            }];
        }
        else
        {
            NSError *error = [NSError errorWithDomain:@"db" code:0 userInfo:@{NSLocalizedDescriptionKey:@"db save context fault"}];
            completionBlock(OperationType_Save, error);
        }
    }

    -(void)deleteDBObject:(id)object completion:(FYHDBOperationCompletionBlock)completionBlock
    {
        NSManagedObjectContext *moc = ((NSManagedObject *)object).managedObjectContext;
        __block NSError *error;

        [moc performBlockAndWait:^{
            [moc deleteObject:object];
            [moc save:&error];
            completionBlock(OperationType_Delete, error);
        }];
    }

    #pragma mark -

    // Returns the managed object context for the application.
    // If the context doesn't already exist, it is created and bound to the persistent store coordinator for the application.
    - (NSManagedObjectContext *)mainManagedObjectContext
    {
        if (__mainManagedObjectContext) {
            return __mainManagedObjectContext;
        }

        NSManagedObjectContext * __managedObjectContext = nil;

        NSPersistentStoreCoordinator *coordinator = [self persistentStoreCoordinator];
        if (coordinator != nil)
        {
            __managedObjectContext = [[NSManagedObjectContext alloc] initWithConcurrencyType:NSMainQueueConcurrencyType];
            [__managedObjectContext setPersistentStoreCoordinator:coordinator];
        }
        return __managedObjectContext;
    }

    // Returns the managed object model for the application.
    // If the model doesn't already exist, it is created from the application's model.
    - (NSManagedObjectModel *)managedObjectModel
    {
        if (__managedObjectModel != nil) {
            return __managedObjectModel;
        }
        NSURL *modelURL = [[NSBundle mainBundle] URLForResource:NAME_OF_MODELD withExtension:@"momd"];
        __managedObjectModel = [[NSManagedObjectModel alloc] initWithContentsOfURL:modelURL];
        return __managedObjectModel;
    }

    // Returns the persistent store coordinator for the application.
    // If the coordinator doesn't already exist, it is created and the application's store added to it.
    - (NSPersistentStoreCoordinator *)persistentStoreCoordinator
    {
        if (__persistentStoreCoordinator != nil)
        {
            return __persistentStoreCoordinator;
        }

        NSURL *storeURL = [[self applicationDocumentsDirectory] URLByAppendingPathComponent:NAME_OF_SQLITE];
        NSLog(@"AMDBManager storeUrl %@",storeURL);
        NSError *error = nil;

        NSDictionary *options = [NSDictionary dictionaryWithObjectsAndKeys:
                                 [NSNumber numberWithBool:YES], NSMigratePersistentStoresAutomaticallyOption,
                                 [NSNumber numberWithBool:YES], NSInferMappingModelAutomaticallyOption, nil];

        __persistentStoreCoordinator = [[NSPersistentStoreCoordinator alloc] initWithManagedObjectModel:[self managedObjectModel]];
        if (![__persistentStoreCoordinator addPersistentStoreWithType:NSSQLiteStoreType configuration:nil URL:storeURL options:options error:&error])
        {
            /*
             Replace this implementation with code to handle the error appropriately.

             abort() causes the application to generate a crash log and terminate. You should not use this function in a shipping application, although it may be useful during development. If it is not possible to recover from the error, display an alert panel that instructs the user to quit the application by pressing the Home button.

             Typical reasons for an error here include:
             * The persistent store is not accessible;
             * The schema for the persistent store is incompatible with current managed object model.
             Check the error message to determine what the actual problem was.


             If the persistent store is not accessible, there is typically something wrong with the file path. Often, a file URL is pointing into the application's resources directory instead of a writeable directory.

             If you encounter schema incompatibility errors during development, you can reduce their frequency by:
             * Simply deleting the existing store:
             [[NSFileManager defaultManager] removeItemAtURL:storeURL error:nil]

             * Performing automatic lightweight migration by passing the following dictionary as the options parameter:
             [NSDictionary dictionaryWithObjectsAndKeys:[NSNumber numberWithBool:YES], NSMigratePersistentStoresAutomaticallyOption, [NSNumber numberWithBool:YES], NSInferMappingModelAutomaticallyOption, nil];

             Lightweight migration will only work for a limited set of schema changes; consult "Core Data Model Versioning and Data Migration Programming Guide" for details.

             */
            NSLog(@"Unresolved error %@, %@", error, [error userInfo]);
            abort();
        }

        return __persistentStoreCoordinator;
    }

    #pragma mark - Application's Documents directory

    // Returns the URL to the application's Documents directory.
    - (NSURL *)applicationDocumentsDirectory
    {
        return [[[NSFileManager defaultManager] URLsForDirectory:NSDocumentDirectory inDomains:NSUserDomainMask] lastObject];
    }

    #pragma mark -

    #pragma mark - Internal Method
    - (NSArray *)fetchDataArrayForEntity:(NSString *)entityName
                            byPredicates:(NSPredicate *)predicate
                         sortDescriptors:(NSArray *)sortDescriptiors
                  inManagedObjectContext:(NSManagedObjectContext *)context
    {
        __block NSArray *fetchedObjects = nil;

        if (context == __mainManagedObjectContext)
        {
            [context performBlockAndWait:^{
                NSFetchRequest *fetchRequest = [[NSFetchRequest alloc] init];
                NSEntityDescription *entity = [NSEntityDescription entityForName:entityName inManagedObjectContext:context];
                [fetchRequest setEntity:entity];
                [fetchRequest setPredicate:predicate];
                [fetchRequest setSortDescriptors:sortDescriptiors];

                NSError *error = nil;
                fetchedObjects = [context executeFetchRequest:fetchRequest error:&error];
                if (fetchedObjects == nil || error) {
                    fetchedObjects = nil;
                }
            }];
        }
        else
        {
            NSLog(@"error: fetching from unknown context");
        }

        return fetchedObjects;
    }

    @end

#####用法

DBManager.h

    //
    //  DBManager.h
    //  YLAlbum
    //
    //  Created by FYH on 7/29/14.
    //  Copyright (c) 2014 FYH. All rights reserved.
    //

    #import <Foundation/Foundation.h>
    #import "FYHDBManager.h"
    #import "GJResultItem.h"
    #import "GJPlayer.h"

    @interface DBManager : FYHDBManager

    + (DBManager *)sharedManager;

    #pragma mark - GJResultItem

    - (GJResultItem *)createWithAInfo:(NSString *)aAinfo
                                BInfo:(NSString *)aBinfo
                                FInfo:(NSString *)aFinfo
                                GInfo:(NSString *)aGinfo
                              Version:(NSString *)aVersion
                                 Type:(NSString *)aType;

    - (ResultType)deleteGJResultItemById:(NSString *)aId;
    - (ResultType)updateGJResultItem:(GJResultItem *)aGJResultItem;
    - (GJResultItem *)GJResultItemById:(NSString *)aId;
    - (NSArray *)allGJResultItemsByVersion:(NSString *)aVersion andType:(NSString *)aType;
    - (NSArray *)allGJResultItemsByType:(NSString *)aType;
    - (NSArray *)allGJResultItems;
    - (ResultType)clearallValidGJResultItems;
    - (NSArray *)resultItemByItemA:(NSString *)aAinfo ItemB:(NSString *)aBinfo ItemF:(NSString *)aFinfo  ItemG:(NSString *)aGinfo forVersion:(NSString *)aVersion;
    - (BOOL)isExistItemA:(NSString *)aAinfo ItemB:(NSString *)aBinfo ItemF:(NSString *)aFinfo  ItemG:(NSString *)aGinfo forVersion:(NSString *)aVersion;

    #pragma mark - GJPlayer

    - (GJPlayer *)createGJPlayerWithpRoleInfo:(NSString *)apRoleInfo pName:(NSString *)apName pPhone:(NSString *)apPhone pRole:(NSString *)apRole;
    - (ResultType)deleteGJPlayerById:(NSString *)aId;
    - (GJPlayer *)GJPlayerById:(NSString *)aId;
    - (ResultType)updateGJPlayer:(GJPlayer *)aGJPlayer;
    - (NSArray *)allGJPlayers;
    - (ResultType)clearallValidGJPlayers;

    #pragma mark - GJTestPlayer

    - (GJPlayer *)createGJTestPlayerWithpRoleInfo:(NSString *)apRoleInfo pName:(NSString *)apName pPhone:(NSString *)apPhone pRole:(NSString *)apRole;
    - (ResultType)deleteGJTestPlayerById:(NSString *)aId;
    - (GJPlayer *)GJTestPlayerById:(NSString *)aId;
    - (ResultType)updateGJTestPlayer:(GJPlayer *)aGJPlayer;
    - (NSArray *)allGJTestPlayers;
    - (ResultType)clearallValidGJTestPlayers;

    @end

DBManager.m

    //
    //  DBManager.m
    //  YLAlbum
    //
    //  Created by FYH on 7/29/14.
    //  Copyright (c) 2014 FYH. All rights reserved.
    //

    #import "DBManager.h"

    @implementation DBManager

    + (NSString *)randmIdFor:(NSString *)aTitle {
        NSString *strId = [aTitle stringByAppendingString:[[NSString stringWithFormat:@"%f",[[NSDate date] timeIntervalSince1970]] stringByReplacingOccurrencesOfString:@"." withString:@""]];
        return [strId stringByAppendingString:[NSString stringWithFormat:@"%u",arc4random_uniform(10000)]];
    }

    +(DBManager *)sharedManager
    {
        static DBManager *sharedManager;

        static dispatch_once_t onceToken;
        dispatch_once(&onceToken, ^{
            sharedManager = [[DBManager alloc] init];
        });

        return sharedManager;
    }

    #pragma mark - GJResultItem

    - (GJResultItem *)createWithAInfo:(NSString *)aAinfo
                                BInfo:(NSString *)aBinfo
                                FInfo:(NSString *)aFinfo
                                GInfo:(NSString *)aGinfo
                              Version:(NSString *)aVersion
                                 Type:(NSString *)aType
    {
        NSArray *list  = [self resultItemByItemA:aAinfo ItemB:aBinfo ItemF:aFinfo ItemG:aGinfo forVersion:aVersion];
        if (list && [list count] != 0) {
            return list.firstObject;
        }

        NSManagedObjectContext * __managedObjectContext = [self mainManagedObjectContext];

        GJResultItem *aGJResultItem = (GJResultItem *)[NSEntityDescription insertNewObjectForEntityForName:ENTITY_RESULT_ITEM_NAME
                                                                                       inManagedObjectContext:__managedObjectContext];

        aGJResultItem.rId = [[self class] randmIdFor:@"ResultItem"];
        aGJResultItem.rAInfo = aAinfo;
        aGJResultItem.rBInfo = aBinfo;
        aGJResultItem.rFInfo = aFinfo;
        aGJResultItem.rGInfo = aGinfo;
        aGJResultItem.rVersion = aVersion;
        aGJResultItem.rType = aType;
        aGJResultItem.rCount = @"0";
        aGJResultItem.rTime = [[NSString stringWithFormat:@"%f",[[NSDate date] timeIntervalSince1970]] stringByReplacingOccurrencesOfString:@"." withString:@""];
        aGJResultItem.rEditing = @"0";
        aGJResultItem.rValid = @"1";

        ResultType type = [self updateGJResultItem:aGJResultItem];

        if (type != ResultType_Success) {

            aGJResultItem = nil;

        }

        return aGJResultItem;
    }

    - (ResultType)deleteGJResultItemById:(NSString *)aId
    {
        GJResultItem *GJResultItem = [self GJResultItemById:aId];

        if (!GJResultItem) {
            return ResultType_Not_Exist;
        }

        ResultType type = ResultType_Success;

        [self deleteDBObject:GJResultItem completion:^(NSInteger type, NSError *error) {
            if (error) {
                type = ResultType_Delete_Failed;
                NSLog(@"%ld",(long)type);
                NSLog(@"Error to Create New GJResultItem!");
            }
        }];

        return type;
    }

    - (ResultType)updateGJResultItem:(GJResultItem *)aGJResultItem
    {
        ResultType type = ResultType_Success;

        [self saveManagedObject:aGJResultItem completion:^(NSInteger type, NSError *error) {
            if (error) {
                type = ResultType_Delete_Failed;
                NSLog(@"%ld",(long)type);
                NSLog(@"Error to Delete New GJResultItem!");
            }
        }];

        return type;
    }

    - (GJResultItem *)GJResultItemById:(NSString *)aId
    {
        NSPredicate * filter = [NSPredicate predicateWithFormat:@"rId = %@", aId];
        NSArray *array = [self fetchDataArrayForEntity:ENTITY_RESULT_ITEM_NAME
                                          byPredicates:filter
                                       sortDescriptors:nil
                                inManagedObjectContext:[self mainManagedObjectContext]];
        return array.firstObject;
    }

    - (NSArray *)allGJResultItemsByVersion:(NSString *)aVersion andType:(NSString *)aType
    {
        NSPredicate * filter = nil;

        filter = [NSPredicate predicateWithFormat:@"rVersion = %@", aVersion];

        NSSortDescriptor *sortDescriptor = nil;

        if ([aType isEqualToString:@"1"])
        {
            sortDescriptor = [[NSSortDescriptor alloc] initWithKey:@"rCount" ascending:NO];
        }
        else
        {
            sortDescriptor = [[NSSortDescriptor alloc] initWithKey:@"rAInfo" ascending:YES];
        }

        NSArray *sortDescriptors = [[NSArray alloc] initWithObjects:sortDescriptor,nil];

        NSArray *array = [self fetchDataArrayForEntity:ENTITY_RESULT_ITEM_NAME
                                          byPredicates:filter
                                       sortDescriptors:sortDescriptors
                                inManagedObjectContext:[self mainManagedObjectContext]];
        return array;
    }

    - (NSArray *)allGJResultItemsByType:(NSString *)aType
    {
        NSSortDescriptor *sortDescriptor = [[NSSortDescriptor alloc] initWithKey:@"rTime" ascending:NO];
        NSArray *sortDescriptors = [[NSArray alloc] initWithObjects:sortDescriptor,nil];

        NSPredicate * filter = [NSPredicate predicateWithFormat:@"rType = %@",aType];
        NSArray *array = [self fetchDataArrayForEntity:ENTITY_RESULT_ITEM_NAME
                                          byPredicates:filter
                                       sortDescriptors:sortDescriptors
                                inManagedObjectContext:[self mainManagedObjectContext]];
        return array;
    }

    - (NSArray *)allGJResultItems
    {
        NSFetchRequest *request = [[NSFetchRequest alloc] init];

        NSEntityDescription *myEntityQuery = [NSEntityDescription
                                              entityForName:ENTITY_RESULT_ITEM_NAME
                                              inManagedObjectContext:[self mainManagedObjectContext]];

        [request setEntity:myEntityQuery];

        NSSortDescriptor *sortDescriptor = [[NSSortDescriptor alloc] initWithKey:@"rTime" ascending:NO];
        NSArray *sortDescriptors = [[NSArray alloc] initWithObjects:sortDescriptor,nil];
        [request setSortDescriptors:sortDescriptors];

        NSError *error =nil;
        NSArray *DeviceArr = [[self mainManagedObjectContext] executeFetchRequest:request error:&error];
        return DeviceArr;
    }

    - (ResultType)clearallValidGJResultItems
    {
        NSFetchRequest *fetch = [[NSFetchRequest alloc] init];

        [fetch setEntity:[NSEntityDescription entityForName:ENTITY_RESULT_ITEM_NAME inManagedObjectContext:[self mainManagedObjectContext]]];

        ResultType type = ResultType_Success;

        NSError *error =nil;
        NSArray *dbList = [[self mainManagedObjectContext] executeFetchRequest:fetch error:&error];
        if (error) {
            type = ResultType_Fetch_Failed;
            NSLog(@"Error to Create New GJResultItem!");
        }

        for (id object in dbList) {
            [self deleteDBObject:object completion:^(NSInteger type, NSError *error) {
                if (error) {
                    type = ResultType_Clear_Failed;
                    NSLog(@"%ld",(long)type);
                    NSLog(@"Error to Create New GJResultItem!");
                }
            }];
        }

        return type;
    }

    - (NSArray *)resultItemByItemA:(NSString *)aAinfo ItemB:(NSString *)aBinfo ItemF:(NSString *)aFinfo  ItemG:(NSString *)aGinfo forVersion:(NSString *)aVersion
    {
        NSPredicate *filter = [NSPredicate predicateWithFormat:@"rVersion = %@  && rAInfo = %@  && rBInfo = %@  && rFInfo = %@ && rGInfo = %@", aVersion,aAinfo,aBinfo,aFinfo,aGinfo];

        NSSortDescriptor *sortDescriptor = [[NSSortDescriptor alloc] initWithKey:@"rTime" ascending:NO];
        NSArray *sortDescriptors = [[NSArray alloc] initWithObjects:sortDescriptor,nil];

        NSArray *array = [self fetchDataArrayForEntity:ENTITY_RESULT_ITEM_NAME
                                          byPredicates:filter
                                       sortDescriptors:sortDescriptors
                                inManagedObjectContext:[self mainManagedObjectContext]];

        return array;
    }

    - (BOOL)isExistItemA:(NSString *)aAinfo ItemB:(NSString *)aBinfo ItemF:(NSString *)aFinfo  ItemG:(NSString *)aGinfo forVersion:(NSString *)aVersion
    {
        NSArray *array = [self resultItemByItemA:aAinfo ItemB:aBinfo ItemF:aFinfo ItemG:aGinfo forVersion:aVersion];
        return (array && array.count > 0);
    }

    #pragma mark - GJPlayer

    - (GJPlayer *)createGJPlayerWithpRoleInfo:(NSString *)apRoleInfo pName:(NSString *)apName pPhone:(NSString *)apPhone pRole:(NSString *)apRole
    {
        NSManagedObjectContext * __managedObjectContext = [self mainManagedObjectContext];

        GJPlayer *aGJPlayer = (GJPlayer *)[NSEntityDescription insertNewObjectForEntityForName:ENTITY_GJPLAYER_NAME
                                                                     inManagedObjectContext:__managedObjectContext];

        aGJPlayer.pId = [[self class] randmIdFor:@"Player"];
        aGJPlayer.pRoleInfo = apRoleInfo;
        aGJPlayer.pName = apName;
        aGJPlayer.pPhone = apPhone;
        aGJPlayer.pRole = apRole;
        aGJPlayer.pDirection = @"0";
        aGJPlayer.pType = @"Normal";

        ResultType type = [self updateGJPlayer:aGJPlayer];

        if (type != ResultType_Success) {

            aGJPlayer = nil;
        }

        return aGJPlayer;
    }

    - (ResultType)deleteGJPlayerById:(NSString *)aId
    {
        GJPlayer *aPlayer = [self GJPlayerById:aId];

        if (!aPlayer) {
            return ResultType_Not_Exist;
        }

        ResultType type = ResultType_Success;

        [self deleteDBObject:aPlayer completion:^(NSInteger type, NSError *error) {
            if (error) {
                type = ResultType_Delete_Failed;
                NSLog(@"%ld",(long)type);
                NSLog(@"Error to Create New aPlayer!");
            }
        }];

        return type;
    }

    - (GJPlayer *)GJPlayerById:(NSString *)aId
    {
        NSPredicate * filter = [NSPredicate predicateWithFormat:@"pId = %@", aId];
        NSArray *array = [self fetchDataArrayForEntity:ENTITY_GJPLAYER_NAME
                                          byPredicates:filter
                                       sortDescriptors:nil
                                inManagedObjectContext:[self mainManagedObjectContext]];
        return array.firstObject;
    }

    - (ResultType)updateGJPlayer:(GJPlayer *)aGJPlayer
    {
        ResultType type = ResultType_Success;

        [self saveManagedObject:aGJPlayer completion:^(NSInteger type, NSError *error) {
            if (error) {
                type = ResultType_Save_Failed;
                NSLog(@"Error to Delete New GJPlayer!");
            }
        }];

        return type;
    }

    - (NSArray *)allGJPlayers
    {
        NSFetchRequest *request = [[NSFetchRequest alloc] init];

        NSEntityDescription *myEntityQuery = [NSEntityDescription
                                              entityForName:ENTITY_GJPLAYER_NAME
                                              inManagedObjectContext:[self mainManagedObjectContext]];

        [request setEntity:myEntityQuery];

        NSSortDescriptor *sortDescriptor = [[NSSortDescriptor alloc] initWithKey:@"pId" ascending:NO];
        NSArray *sortDescriptors = [[NSArray alloc] initWithObjects:sortDescriptor,nil];
        [request setSortDescriptors:sortDescriptors];

         NSPredicate * filter = [NSPredicate predicateWithFormat:@"pType = %@", @"Normal"];
        [request setPredicate:filter ];

        NSError *error =nil;
        NSArray *GJPlayerArr = [[self mainManagedObjectContext] executeFetchRequest:request error:&error];
        return GJPlayerArr;
    }

    - (ResultType)clearallValidGJPlayers
    {
        NSFetchRequest *fetch = [[NSFetchRequest alloc] init];

        [fetch setEntity:[NSEntityDescription entityForName:ENTITY_GJPLAYER_NAME inManagedObjectContext:[self mainManagedObjectContext]]];

        NSPredicate * filter = [NSPredicate predicateWithFormat:@"pType = %@", @"Normal"];
        [fetch setPredicate:filter ];

        ResultType type = ResultType_Success;

        NSError *error =nil;
        NSArray *dbList = [[self mainManagedObjectContext] executeFetchRequest:fetch error:&error];
        if (error) {
            type = ResultType_Fetch_Failed;
            NSLog(@"Error to Create New GJPlayer!");
        }

        for (id object in dbList) {
            [self deleteDBObject:object completion:^(NSInteger type, NSError *error) {
                if (error) {
                    type = ResultType_Clear_Failed;
                    NSLog(@"Error to Create New GJPlayer!");
                }
            }];
        }

        return type;
    }

    #pragma mark - GJTestPlayer

    - (GJPlayer *)createGJTestPlayerWithpRoleInfo:(NSString *)apRoleInfo pName:(NSString *)apName pPhone:(NSString *)apPhone pRole:(NSString *)apRole
    {
        NSManagedObjectContext * __managedObjectContext = [self mainManagedObjectContext];

        GJPlayer *aGJPlayer = (GJPlayer *)[NSEntityDescription insertNewObjectForEntityForName:ENTITY_GJPLAYER_NAME
                                                                        inManagedObjectContext:__managedObjectContext];

        aGJPlayer.pId = [[self class] randmIdFor:@"Player"];
        aGJPlayer.pRoleInfo = apRoleInfo;
        aGJPlayer.pName = apName;
        aGJPlayer.pPhone = apPhone;
        aGJPlayer.pRole = apRole;
        aGJPlayer.pDirection = @"0";
        aGJPlayer.pType = @"Test";

        ResultType type = [self updateGJPlayer:aGJPlayer];

        if (type != ResultType_Success) {

            aGJPlayer = nil;
        }

        return aGJPlayer;
    }

    - (ResultType)deleteGJTestPlayerById:(NSString *)aId
    {
        return [self deleteGJPlayerById:aId];
    }

    - (GJPlayer *)GJTestPlayerById:(NSString *)aId
    {
        return [self GJPlayerById:aId];
    }

    - (ResultType)updateGJTestPlayer:(GJPlayer *)aGJPlayer
    {
        return [self updateGJPlayer:aGJPlayer];
    }

    - (NSArray *)allGJTestPlayers
    {
        NSFetchRequest *request = [[NSFetchRequest alloc] init];

        NSEntityDescription *myEntityQuery = [NSEntityDescription
                                              entityForName:ENTITY_GJPLAYER_NAME
                                              inManagedObjectContext:[self mainManagedObjectContext]];

        [request setEntity:myEntityQuery];

        NSSortDescriptor *sortDescriptor = [[NSSortDescriptor alloc] initWithKey:@"pId" ascending:NO];
        NSArray *sortDescriptors = [[NSArray alloc] initWithObjects:sortDescriptor,nil];
        [request setSortDescriptors:sortDescriptors];

        NSPredicate * filter = [NSPredicate predicateWithFormat:@"pType = %@", @"Test"];
        [request setPredicate:filter ];

        NSError *error =nil;
        NSArray *GJPlayerArr = [[self mainManagedObjectContext] executeFetchRequest:request error:&error];
        return GJPlayerArr;
    }

    - (ResultType)clearallValidGJTestPlayers
    {
        NSFetchRequest *fetch = [[NSFetchRequest alloc] init];

        [fetch setEntity:[NSEntityDescription entityForName:ENTITY_GJPLAYER_NAME inManagedObjectContext:[self mainManagedObjectContext]]];

        NSPredicate * filter = [NSPredicate predicateWithFormat:@"pType = %@", @"Test"];
        [fetch setPredicate:filter ];

        ResultType type = ResultType_Success;

        NSError *error =nil;
        NSArray *dbList = [[self mainManagedObjectContext] executeFetchRequest:fetch error:&error];
        if (error) {
            type = ResultType_Fetch_Failed;
            NSLog(@"Error to Create New GJPlayer!");
        }

        for (id object in dbList) {
            [self deleteDBObject:object completion:^(NSInteger type, NSError *error) {
                if (error) {
                    type = ResultType_Clear_Failed;
                    NSLog(@"Error to Create New GJPlayer!");
                }
            }];
        }

        return type;
    }

    @end

### 效果图
（无）

### 备注
（无）
