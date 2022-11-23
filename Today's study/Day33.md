#### for문
```node
for (int i = 0; i < rvmReviews.size(); i++) {
    Review singleReview = rvmReviews.get(i);
    // 단일 리뷰에서 rvwSeqNo 넘기기
    List<ReviewImg> reviewImgList = reviewMapper.getReviewImgList(singleReview.getRvwSeqNo());
}
```
+ `i` 얘가 단일 리뷰

#### 향상된 for문
```node
for(Review review : rvmReviews) {
   // 단일 리뷰에서 rvwSeqNo 넘기기
   List<ReviewImg> reviewImgList = reviewMapper.getReviewImgList(review.getRvwSeqNo());
}
```
+ `review`얘가 단일 리뷰


#### foreach문
```node
rvmReviews.forEach(review -> {
            
    List<ReviewImg> reviewImgList = reviewMapper.getReviewImgList(review.getRvwSeqNo());

});
```
+ `review` 얘가 단일 리뷰
+ `foreach문`이 가장 효율적이다.

        
