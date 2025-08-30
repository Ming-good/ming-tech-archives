```java
@Test  
public void getCityTest() {  
StepVerifier  
	.create(RecordTestEx.getCapitalizedCountry(  
		Flux.just("korea", "england", "canada", "india")  
	))  
	.expectSubscription()  
	.recordWith(ArrayList::new)  
	.thenConsumeWhile(country -> !country.isEmpty())  
	.consumeRecordedWith(countiries -> {  
		assertEquals(  
			countiries  
				.stream()  
				.allMatch(country ->  
				Character.isUpperCase(country.charAt(0))), true  
			);  
		}
	)  
	.expectComplete()  
	.verify();  
}  
  
@Test  
public void getCountryTest() {  
StepVerifier  
	.create(RecordTestEx.getCapitalizedCountry(  
		Flux.just("korea", "england", "canada", "india")  
	))  
	.expectSubscription()  
	.recordWith(ArrayList::new)  
	.thenConsumeWhile(country -> !country.isEmpty())  
	.expectRecordedMatches(coutries ->{ 
		return coutries  
			.stream()  
			.allMatch(country ->  
			Character.isUpperCase(country.charAt(0)));  
		}  
	)  
	.expectComplete()  
	.verify();
```
- `recoredWith()`: 파라미터로 전달한 Java컬렉션에 emit된 데이터를 추가함
- `consumeRecoredWith()`: 컬렉션에 기록된 데이터를 소비한다.
- `expectRecordedMatches()`: 메서드 내에서 `Predecate`를 사용하여 컬렉션에 기록된 모든 데이터를 매칭함