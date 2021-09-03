# 번외편 - Spring MVC가 리퀘스트 맵핑에서 활용하고 있는 리플렉션

# RequestMapping

```java
org.springframework.web.servlet.handler.AbstractHandlerMethodMapping

protected void detectHandlerMethods(Object handler) {
		Class<?> handlerType = (handler instanceof String ?
				obtainApplicationContext().getType((String) handler) : handler.getClass());

		if (handlerType != null) {
			Class<?> userType = ClassUtils.getUserClass(handlerType);
			Map<Method, T> methods = MethodIntrospector.selectMethods(userType,
					(MethodIntrospector.MetadataLookup<T>) method -> {
						try {
							return getMappingForMethod(method, userType);
						}
						catch (Throwable ex) {
							throw new IllegalStateException("Invalid mapping on handler class [" +
									userType.getName() + "]: " + method, ex);
						}
					});
			if (logger.isTraceEnabled()) {
				logger.trace(formatMappings(userType, methods));
			}
			else if (mappingsLogger.isDebugEnabled()) {
				mappingsLogger.debug(formatMappings(userType, methods));
			}
			methods.forEach((method, mapping) -> {
				Method invocableMethod = AopUtils.selectInvocableMethod(method, userType);
				registerHandlerMethod(handler, invocableMethod, mapping);
			});
		}
	}
```

```java
org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping
RequestMappingInfo getMappingForMethod(Method method, Class<?> handlerType)

@Override
@Nullable
protected RequestMappingInfo getMappingForMethod(Method method, Class<?> handlerType) {
	RequestMappingInfo info = createRequestMappingInfo(method);
	if (info != null) {
		RequestMappingInfo typeInfo = createRequestMappingInfo(handlerType);
		if (typeInfo != null) {
			info = typeInfo.combine(info);
		}
		String prefix = getPathPrefix(handlerType);
		if (prefix != null) {
			info = RequestMappingInfo.paths(prefix).options(this.config).build().combine(info);
		}
	}
	return info;
}

@Nullable
private RequestMappingInfo createRequestMappingInfo(AnnotatedElement element) {
	RequestMapping requestMapping = AnnotatedElementUtils.findMergedAnnotation(element, RequestMapping.class);
	RequestCondition<?> condition = (element instanceof Class ?
			getCustomTypeCondition((Class<?>) element) : getCustomMethodCondition((Method) element));
	return (requestMapping != null ? createRequestMappingInfo(requestMapping, condition) : null);
}

protected RequestMappingInfo createRequestMappingInfo(
	RequestMapping requestMapping, @Nullable RequestCondition<?> customCondition) {

	RequestMappingInfo.Builder builder = RequestMappingInfo
			.paths(resolveEmbeddedValuesInPatterns(requestMapping.path()))
			.methods(requestMapping.method())
			.params(requestMapping.params())
			.headers(requestMapping.headers())
			.consumes(requestMapping.consumes())
			.produces(requestMapping.produces())
			.mappingName(requestMapping.name());
	if (customCondition != null) {
		builder.customCondition(customCondition);
	}
	return builder.options(this.config).build();
}
```

```java
org.springframework.core.MethodIntrospector

// MetadataLookup 로 getMappingInfo가 전달됨
public static <T> Map<Method, T> selectMethods(Class<?> targetType, final MetadataLookup<T> metadataLookup) {
		final Map<Method, T> methodMap = new LinkedHashMap<>();
		Set<Class<?>> handlerTypes = new LinkedHashSet<>();
		Class<?> specificHandlerType = null;

		if (!Proxy.isProxyClass(targetType)) {
			specificHandlerType = ClassUtils.getUserClass(targetType);
			handlerTypes.add(specificHandlerType);
		}
		handlerTypes.addAll(ClassUtils.getAllInterfacesForClassAsSet(targetType));

		for (Class<?> currentHandlerType : handlerTypes) {
			final Class<?> targetClass = (specificHandlerType != null ? specificHandlerType : currentHandlerType);

			ReflectionUtils.doWithMethods(currentHandlerType, method -> {
				Method specificMethod = ClassUtils.getMostSpecificMethod(method, targetClass);
				T result = metadataLookup.inspect(specificMethod);
				if (result != null) {
					Method bridgedMethod = BridgeMethodResolver.findBridgedMethod(specificMethod);
					if (bridgedMethod == specificMethod || metadataLookup.inspect(bridgedMethod) == null) {
						methodMap.put(specificMethod, result);
					}
				}
			}, ReflectionUtils.USER_DECLARED_METHODS);
		}

		return methodMap;
	}
```

```java
org.springframework.util.ClassUtils

public static Class<?> getUserClass(Class<?> clazz) {
		if (clazz.getName().contains(CGLIB_CLASS_SEPARATOR)) {
			Class<?> superclass = clazz.getSuperclass();
			if (superclass != null && superclass != Object.class) {
				return superclass;
			}
		}
		return clazz;
	}
```

```java
org.springframework.util.ReflectionUtils

public static void doWithMethods(Class<?> clazz, MethodCallback mc, @Nullable MethodFilter mf) {
		// Keep backing up the inheritance hierarchy.
		Method[] methods = getDeclaredMethods(clazz, false);
		for (Method method : methods) {
			if (mf != null && !mf.matches(method)) {
				continue;
			}
			try {
				mc.doWith(method);
			}
			catch (IllegalAccessException ex) {
				throw new IllegalStateException("Not allowed to access method '" + method.getName() + "': " + ex);
			}
		}
		if (clazz.getSuperclass() != null && (mf != USER_DECLARED_METHODS || clazz.getSuperclass() != Object.class)) {
			doWithMethods(clazz.getSuperclass(), mc, mf);
		}
		else if (clazz.isInterface()) {
			for (Class<?> superIfc : clazz.getInterfaces()) {
				doWithMethods(superIfc, mc, mf);
			}
		}
	}
```

```java
org.springframework.data.util.AnnotationDetectionMethodCallback

@Override
public void doWith(Method method) throws IllegalArgumentException, IllegalAccessException {

	if (foundMethod != null && !enforceUniqueness) {
		return;
	}

	A foundAnnotation = AnnotatedElementUtils.findMergedAnnotation(method, annotationType);

	if (foundAnnotation != null) {

		if (foundMethod != null && enforceUniqueness) {
			throw new IllegalStateException(
					String.format(MULTIPLE_FOUND, foundAnnotation.getClass().getName(), foundMethod, method));
		}

		this.annotation = foundAnnotation;
		this.foundMethod = method;
	}
}
```
