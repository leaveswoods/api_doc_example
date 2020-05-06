```
/**
 * @api {post} /api/v1/payments/stripe - Query for coupon and return total discount
 * @apiName ValidateCoupon
 * @apiVersion 1.0.0
 * @apiGroup Payment
 *
 * @apiParam {String} courseId
 *
 * @apiSuccess {String} discount - Discount of coupon
 * @apiError InvalidCoupon - The <code>courseId</code> of the coupon is not found, or coupon is not linked to course
 */
const validateCoupon = async (req, res) => {
  const couponId = req.params.id;
  const { courseId } = req.params;
  try {
    const coupon = await stripeClient.coupons.retrieve(couponId);
    let isLinkedToCourse = true;
    if (coupon.metadata.courseIds) {
      const courseIds = coupon.metadata.courseIds.split(',');
      isLinkedToCourse = courseIds.includes(courseId);
    }
    if (!coupon || !isLinkedToCourse) {
      return res.status(404).send({ error: { message: 'Invalid coupon' } });
    }
    return res.json({ discount: coupon.percent_off });
  } catch (error) {
    let message = error
    if (error.message) {
      message = error.message
    }
    return res.status(500).send({ error: { message: error.message } });
  }
}
```# api_doc_example
