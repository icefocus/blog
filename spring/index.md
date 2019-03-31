1、动态生成bean
BeanfactoryUtils.beanNamesForTypeIncludingAncestors
Get all bean names for the given type, including those defined in ancestor factories.
BeanFactoryUtils
					.beanNamesForTypeIncludingAncestors(applicationContext,
							AuthenticationManager.class);

private static void validateBeanCycle(Object auth, Set<String> beanNames) {
  if (auth != null && !beanNames.isEmpty()) {
    if (auth instanceof Advised) {
      Advised advised = (Advised) auth;
      TargetSource targetSource = advised.getTargetSource();
      if (targetSource instanceof LazyInitTargetSource) {
        LazyInitTargetSource lits = (LazyInitTargetSource) targetSource;
        if (beanNames.contains(lits.getTargetBeanName())) {
          throw new FatalBeanException(
              "A dependency cycle was detected when trying to resolve the AuthenticationManager. Please ensure you have configured authentication.");
        }
      }
    }
    beanNames = Collections.emptySet();
  }
}
  
