# Ranking Losses 

## Pair Wise Ranking Loss 

```py
import tensorflow as tf
from typing import Tuple

def pairwise_ranking_loss(y_true: tf.Tensor, y_pred: tf.Tensor, margin: float = 1.0) -> tf.Tensor:
    """
    Computes the pairwise ranking loss for a batch of pairs.

    Args:
        y_true: Tensor of true labels (0 for negative pairs, 1 for positive pairs).
        y_pred: Tensor of predicted similarities/distances, expected to be a tensor of shape (batch_size, 2, embedding_dim) where 
                y_pred[:, 0] is the anchor and y_pred[:, 1] is the positive/negative.
        margin: Margin parameter for the pairwise ranking loss.

    Returns:
        loss: Computed pairwise ranking loss as a scalar tensor.
    """
    anchor, positive_or_negative = y_pred[:, 0], y_pred[:, 1]

    distances = tf.reduce_sum(tf.square(anchor - positive_or_negative), axis=-1)
    positive_loss = y_true * distances
    negative_loss = (1 - y_true) * tf.maximum(margin - distances, 0.0)
    
    loss = positive_loss + negative_loss
    return tf.reduce_mean(loss)

# Example usage:
# model.compile(optimizer='adam', loss=pairwise_ranking_loss)
```

## Triplet Loss 

```py
import tensorflow as tf
from typing import Tuple

def triplet_loss_func(y_true: tf.Tensor, y_pred: tf.Tensor, alpha: float = 0.3) -> tf.Tensor:
    """
    Computes the triplet loss for a batch of triplets.

    Args:
        y_true: True values of classification (unused in this implementation, typically required for compatibility with Keras).
        y_pred: Predicted values, expected to be a tensor of shape (batch_size, 3, embedding_dim) where 
                y_pred[:, 0] is the anchor, y_pred[:, 1] is the positive, and y_pred[:, 2] is the negative.
        alpha: Margin parameter for the triplet loss.

    Returns:
        loss: Computed triplet loss as a scalar tensor.
    """
    anchor, positive, negative = y_pred[:, 0], y_pred[:, 1], y_pred[:, 2]

    positive_dist = tf.reduce_sum(tf.square(anchor - positive), axis=-1)
    negative_dist = tf.reduce_sum(tf.square(anchor - negative), axis=-1)

    loss = tf.maximum(positive_dist - negative_dist + alpha, 0.0)
    return tf.reduce_mean(loss)

# Example usage:
# model.compile(optimizer='adam', loss=triplet_loss_func)
```
