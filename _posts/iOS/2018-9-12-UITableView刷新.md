# UITableView刷新

自己写TestUITableView测试，结论：

1. reloadData：本身就防抖，在极短时间重复调用的情况下；

2. reloadSections：在部分数据刷新时调用，但是得保证：仅仅传入section数据变更，如果其他section的数据也变了（无论哪个线程），闪退

```c
    // load section2Data
    dispatch_async(dispatch_get_global_queue(0, 0), ^{
        NSLog(@"sleep 1");
        sleep(2);
        NSLog(@"wakeup 1");
        
        // self.section3Data = @[@"3-1", @"3-2", @"3-3"]; // crash
        
        dispatch_async(dispatch_get_main_queue(), ^{
            NSLog(@"update section 1");
            self.section2Data = @[@"2-1", @"2-2"];

            // self.section3Data = @[@"3-1", @"3-2", @"3-3"]; // crash

            [self.tableView reloadSections:[NSIndexSet indexSetWithIndex:1] withRowAnimation:UITableViewRowAnimationAutomatic];
            // [self.tableView insertRowsAtIndexPaths:@[[NSIndexPath indexPathForRow:1 inSection:1]] withRowAnimation:UITableViewRowAnimationAutomatic];
        });
    });
```

3. 在保证第2点的情况下，主线程异步多次执行部分刷新不会闪退

```c
- (void)loadData2 {
    // load section1Data
    dispatch_async(dispatch_get_global_queue(0, 0), ^{
        NSLog(@"sleep 0");
        sleep(2);
        NSLog(@"wakeup 0");
        dispatch_async(dispatch_get_main_queue(), ^{
            NSLog(@"update section 0");
            self.section1Data = @[@"1-1"];
            [self.tableView reloadSections:[NSIndexSet indexSetWithIndex:0] withRowAnimation:UITableViewRowAnimationAutomatic];
        });
    });
    
    // load section2Data
    dispatch_async(dispatch_get_global_queue(0, 0), ^{
        NSLog(@"sleep 1");
        sleep(2);
        NSLog(@"wakeup 1");
        
//        self.section3Data = @[@"3-1", @"3-2", @"3-3"]; // crash
        
        dispatch_async(dispatch_get_main_queue(), ^{
            NSLog(@"update section 1");
            self.section2Data = @[@"2-1", @"2-2"];
            
//            self.section3Data = @[@"3-1", @"3-2", @"3-3"]; // crash
            
            [self.tableView reloadSections:[NSIndexSet indexSetWithIndex:1] withRowAnimation:UITableViewRowAnimationAutomatic];
            //        [self.tableView insertRowsAtIndexPaths:@[[NSIndexPath indexPathForRow:1 inSection:1]] withRowAnimation:UITableViewRowAnimationAutomatic];
        });
    });
    
    // load section3Data
    dispatch_async(dispatch_get_global_queue(0, 0), ^{
        NSLog(@"sleep 2");
        sleep(2);
        NSLog(@"wakeup 2");
        dispatch_async(dispatch_get_main_queue(), ^{
            NSLog(@"update section 2");
            self.section3Data = @[@"3-1", @"3-2", @"3-3"];
            [self.tableView reloadSections:[NSIndexSet indexSetWithIndex:2] withRowAnimation:UITableViewRowAnimationAutomatic];
        });
    });
    
    // load section4Data
    dispatch_async(dispatch_get_global_queue(0, 0), ^{
        NSLog(@"sleep 3");
        sleep(2);
        NSLog(@"wakeup 3");
        dispatch_async(dispatch_get_main_queue(), ^{
            NSLog(@"update section 3");
            self.section4Data = @[@"4-1", @"4-2", @"4-3", @"4-4"];
            [self.tableView reloadSections:[NSIndexSet indexSetWithIndex:3] withRowAnimation:UITableViewRowAnimationAutomatic];
        });
    });
    
    // load section5Data
    dispatch_async(dispatch_get_global_queue(0, 0), ^{
        NSLog(@"sleep 4");
        sleep(2);
        NSLog(@"wakeup 4");
        dispatch_async(dispatch_get_main_queue(), ^{
            NSLog(@"update section 4");
            self.section5Data = @[@"5-1", @"5-2", @"5-3", @"5-4", @"5-5"];
            [self.tableView reloadSections:[NSIndexSet indexSetWithIndex:4] withRowAnimation:UITableViewRowAnimationAutomatic];
        });
    });
}
```