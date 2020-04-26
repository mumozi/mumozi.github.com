

# 分类管理模块

## 建表准备

```sql
DROP TABLE IF EXISTS `mmall_category`;
CREATE TABLE `mmall_category` (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT '类别Id',
  `parent_id` int(11) DEFAULT NULL COMMENT '父类别id当id=0时说明是根节点,一级类别',
  `name` varchar(50) DEFAULT NULL COMMENT '类别名称',
  `status` tinyint(1) DEFAULT '1' COMMENT '类别状态1-正常,2-已废弃',
  `sort_order` int(4) DEFAULT NULL COMMENT '排序编号,同类展示顺序,数值相等则自然排序',
  `create_time` datetime DEFAULT NULL COMMENT '创建时间',
  `update_time` datetime DEFAULT NULL COMMENT '更新时间',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=100032 DEFAULT CHARSET=utf8;
```

## 添加分类＆更新品类名字

### 调用层

`src/main/java/com/mmal/controller/backend/CategoryManageController.java`

```java
package com.mmal.controller.backend;

import com.mmal.common.Const;
import com.mmal.common.ResponseCode;
import com.mmal.common.ServerResponse;
import com.mmal.pojo.User;
import com.mmal.service.ICategoryService;
import com.mmal.service.IUserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

import javax.servlet.http.HttpSession;

/**
 * @description:分类
 * @author: MayDay
 * @time: 2020/4/24 16:43
 */
@RestController
@RequestMapping("/manage/category")
public class CategoryManageController {

    @Autowired
    private IUserService iUserService;

    @Autowired
    private ICategoryService iCategoryService;

    /**
     * 添加品类
     *
     * @param session
     * @param categoryName
     * @param parentId
     * @return
     */
    @RequestMapping("add_category.do")
    public ServerResponse addCategory(HttpSession session, String categoryName, @RequestParam(value = "parentId", defaultValue = "0") int parentId) {
        User user = (User) session.getAttribute(Const.CURRENT_USER);
        if (user == null) {
            return ServerResponse.createByErrorCodeMessage(ResponseCode.NEED_LOGIN.getCode(), "用户未登录，请登录");
        }
        //校验一下是否是管理员
        if (iUserService.checkAdminRole(user).isSuccess()) {
            //管理员
            //增加我们处理分类的逻辑
            return iCategoryService.addCategory(categoryName, parentId);
        } else {
            return ServerResponse.createByErrorMessage("无权限操作，需要管理员权限");
        }
    }

    /**
     * 更新品类名字
     *
     * @param session
     * @param categoryId
     * @param categoryName
     * @return
     */
    @RequestMapping("set_category_name.do")
    public ServerResponse setCategoryName(HttpSession session, Integer categoryId, String categoryName) {
        User user = (User) session.getAttribute(Const.CURRENT_USER);
        if (user == null) {
            return ServerResponse.createByErrorCodeMessage(ResponseCode.NEED_LOGIN.getCode(), "用户未登录，请登录");
        }
        //校验一下是否是管理员
        if (iUserService.checkAdminRole(user).isSuccess()) {
            //管理员
            return iCategoryService.updateCategoryName(categoryId, categoryName);
        } else {
            return ServerResponse.createByErrorMessage("无权限操作，需要管理员权限");
        }
    }
}
```

### 接口类

```java
package com.mmal.service;

import com.mmal.common.ServerResponse;

/**
 * @description:
 * @author: MayDay
 * @time: 2020/4/24 17:01
 */
public interface ICategoryService {

    ServerResponse addCategory(String categoryName, Integer parentId);

    ServerResponse updateCategoryName(Integer categoryId, String categoryName);
}

```

### 接口实现类

`src/main/java/com/mmal/service/Impl/CategoryServiceImpl.java`

```java
package com.mmal.service.Impl;

import com.mmal.common.ServerResponse;
import com.mmal.dao.CategoryMapper;
import com.mmal.pojo.Category;
import com.mmal.service.ICategoryService;
import org.apache.commons.lang3.StringUtils;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

/**
 * @description:
 * @author: MayDay
 * @time: 2020/4/24 17:01
 */
@Service("iCategoryService")
public class CategoryServiceImpl implements ICategoryService {

    @Autowired
    private CategoryMapper categoryMapper;

    /**
     * 添加品类
     *
     * @param categoryName
     * @param parentId
     * @return
     */
    public ServerResponse addCategory(String categoryName, Integer parentId) {
        if (parentId == null && StringUtils.isBlank(categoryName)) {
            return ServerResponse.createByErrorMessage("添加品类参数错误");
        }
        Category category = new Category();
        category.setName(categoryName);
        category.setParentId(parentId);
        category.setStatus(true);//这个分类是可用的

        int rowCount = categoryMapper.insert(category);
        if (rowCount > 0) {
            return ServerResponse.createBySuccess("添加品类成功");
        }
        return ServerResponse.createByErrorMessage("添加品类失败");
    }

    /**
     * 更新品类名字
     *
     * @param categoryId
     * @param categoryName
     * @return
     */
    public ServerResponse updateCategoryName(Integer categoryId, String categoryName) {
        if (categoryId == null && StringUtils.isBlank(categoryName)) {
            return ServerResponse.createByErrorMessage("更新品类参数错误");
        }
        Category category = new Category();
        category.setId(categoryId);
        category.setName(categoryName);

        int rowCount = categoryMapper.updateByPrimaryKeySelective(category);
        if (rowCount > 0) {
            return ServerResponse.createBySuccess("更新品类名字成功");
        }
        return ServerResponse.createByErrorMessage("更新品类名字失败");
    }
}

```

## 查询节点和递归查找功能

### 调用层

`src/main/java/com/mmal/controller/backend/CategoryManageController.java`

```java
/**
     * 查询子节点的category信息 (平级)
     *
     * @param session
     * @param categoryId
     * @return
     */
    @RequestMapping("get_category.do")
    public ServerResponse getChildrenParallelCategory(HttpSession session, @RequestParam(value = "categoryId", defaultValue = "0") int categoryId) {
        User user = (User) session.getAttribute(Const.CURRENT_USER);
        if (user == null) {
            return ServerResponse.createByErrorCodeMessage(ResponseCode.NEED_LOGIN.getCode(), "用户未登录，请登录");
        }
        //校验一下是否是管理员
        if (iUserService.checkAdminRole(user).isSuccess()) {
            //查询子节点的category信息，并且不递归，保持平级
            return iCategoryService.getChildrenParallelCategory(categoryId);
        } else {
            return ServerResponse.createByErrorMessage("无权限操作，需要管理员权限");
        }
    }

    /**
     * 递归查询本节点的id及孩纸节点的id
     *
     * @param session
     * @param categoryId
     * @return
     */
    @RequestMapping("get_deep_category.do")
    public ServerResponse getCategoryAndDeepChildrenCategory(HttpSession session, @RequestParam(value = "categoryId", defaultValue = "0") int categoryId) {
        User user = (User) session.getAttribute(Const.CURRENT_USER);
        if (user == null) {
            return ServerResponse.createByErrorCodeMessage(ResponseCode.NEED_LOGIN.getCode(), "用户未登录，请登录");
        }
        //校验一下是否是管理员
        if (iUserService.checkAdminRole(user).isSuccess()) {
            //查询当前节点的id＆递归子节点的id
            return iCategoryService.selectCategoryAndChildrenId(categoryId);
        } else {
            return ServerResponse.createByErrorMessage("无权限操作，需要管理员权限");
        }
    }
```

### 接口类

```java
ServerResponse<List<Category>> getChildrenParallelCategory(Integer categoryId);

ServerResponse selectCategoryAndChildrenId(Integer categoryId);
```

### sql查询

`src/main/java/com/mmal/dao/CategoryMapper.java`

```java
List<Category> selectCategoryChildrenByParentId(Integer parentId);
```
`src/main/resources/mappers/CategoryMapper.xml`

```xml
<select id="selectCategoryChildrenByParentId" resultMap="BaseResultMap" parameterType="int">
        select
        <include refid="Base_Column_List"></include>
        from mmall_category
        where parent_id= #{parentId}
    </select>
```

### 接口实现类

`src/main/java/com/mmal/service/Impl/CategoryServiceImpl.java`

```java
 private Logger logger = LoggerFactory.getLogger(CategoryServiceImpl.class);


/**
     * 查询子节点的category信息，并且不递归，保持平级
     *
     * @param categoryId
     * @return
     */
    public ServerResponse<List<Category>> getChildrenParallelCategory(Integer categoryId) {
        List<Category> categoryList = categoryMapper.selectCategoryChildrenByParentId(categoryId);
        if (CollectionUtils.isEmpty(categoryList)) {
            logger.info("未找到当前分类的子分类");
        }
        return ServerResponse.createBySuccess(categoryList);
    }


/**
     * 递归查询本节点的id及孩纸节点的id
     *
     * @param categoryId
     * @return
     */
    public ServerResponse selectCategoryAndChildrenId(Integer categoryId) {
        Set<Category> categorySet = Sets.newHashSet();
        findChildCategory(categorySet, categoryId);

        List<Integer> categoryList = Lists.newArrayList();
        if (categoryId != null) {
            for (Category categoryItem : categorySet) {
                categoryList.add(categoryItem.getId());
            }
        }
        return ServerResponse.createBySuccess(categoryList);
    }

    /**
     * 递归子节点算法
     *
     * @param categorySet
     * @param categoryId
     * @return
     */
    private Set<Category> findChildCategory(Set<Category> categorySet, Integer categoryId) {
        Category category = categoryMapper.selectByPrimaryKey(categoryId);
        if (category != null) {
            categorySet.add(category);
        }
        //查找子节点，递归算法一定要有一个退出的条件
        List<Category> categoryList = categoryMapper.selectCategoryChildrenByParentId(categoryId);
        for (Category categoryItem : categoryList) {
            findChildCategory(categorySet, categoryItem.getId());
        }
        return categorySet;
    }
```

!>注意区别：Lists和Maps是两个工具类, Lists[.](http://guava-libraries.googlecode.com/svn/trunk/javadoc/com/google/common/collect/Lists.html#newArrayList())newArrayList[()](http://guava-libraries.googlecode.com/svn/trunk/javadoc/com/google/common/collect/Lists.html#newArrayList())其实和new ArrayList()几乎一模一样, 唯一它帮你做的(其实是javac帮你做的), 就是自动推导(不是"倒")尖括号里的数据类型. 1.7版本以后都不需要写数据类型了。所以无差别。

!>set对象不是int或者string，所以需要重写equals跟hashCode，在category对象里使用`ctrl+o`调出重写方法。只比较id所以重写的时候只需要勾选id即可。

```java
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Category category = (Category) o;
        return Objects.equals(id, category.id);
    }

    @Override
    public int hashCode() {
        return Objects.hash(id);
    }
```

