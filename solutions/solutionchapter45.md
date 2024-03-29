### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-03-23 | Alfred Jiang | - |

### 方案名称
UISearchBar - 通过 UISearchDisplayViewController 实现全屏搜索显示效果

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
UISearchBar \ UISearchDisplayViewController \ 搜索 \ 查询 \ 全屏搜索

### 需求场景
1. 实现电机 SearchBar 进入全屏搜索模式的场景

### 参考链接
1. [UISearchDisplayController学习笔记](http://blog.csdn.net/zhaoxy_thu/article/details/14214591)

### 详细内容

#####1. 实例化 UISearchDisplayViewController 对象
    @property (nonatomic, strong) UISearchDisplayController *searchController;

    - (void)addSearchBarAndSearchDisplayController {

        UISearchBar *searchBar = [[UISearchBar alloc] initWithFrame:CGRectZero];
        [searchBar sizeToFit];
        searchBar.delegate = self;

        self.tableView.tableHeaderView = searchBar;

        UISearchDisplayController *searchDisplayController = [[UISearchDisplayController alloc] initWithSearchBar:searchBar contentsController:self];
        searchDisplayController.delegate = self;
        searchDisplayController.searchResultsDataSource = self;
        searchDisplayController.searchResultsDelegate = self;

        self.searchController = searchDisplayController;
    }

#####2. 进入全屏搜索模式，需要设置 UISearchBar 的 delegate，并实现 searchBarShouldBeginEditing 方法。在中间执行：
    [self.searchDisplayController setActive:YES animated:YES];

#####3. 实现必要的代理方法
    //在搜索内容改变时调用
    -(BOOL)searchDisplayController:(UISearchDisplayController *)controller shouldReloadTableForSearchString:(NSString *)searchString {
        return YES;
    }

    //在搜索范围改变时调用(可选)
    - (BOOL)searchDisplayController:(UISearchDisplayController *)controller shouldReloadTableForSearchScope:(NSInteger)searchOption {
        return YES;
    }

### 效果图
（无）

### 备注
（无）
