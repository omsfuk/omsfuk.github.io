控制器内引用自己可能会引发问题
如路由中，引用自己控制器

$scope.$apply应该慎用，否则可能会出现同时出现两个脏值检查循环，进而报错，可以通过自定义一个安全方法。
function safeApply(scope) {
    if(!(scope.$$phase || scope.$root.$$phase)) {
    	scope.$apply();
    }
}
以后每次更新的时候，调用此方法，而不是$scope.$apply
