<?php
/*
Plugin Name: WooCommerce Hide Price, Add to Cart, and Quantity for Accessories
Description: Verbergt de prijs, "Add to Cart" knop en het aantal voor producten in de categorie "accessoires".
Version: 1.1
Author: Jean Paul van der Meer
 */

function hide_price_add_to_cart_and_quantity_for_accessories()
{
    if (is_product() && has_term(itw_get_hide_on_terms(), 'product_cat')) {
        ?>
        <style>
            .price {
                display: none !important;
            }
            .single_add_to_cart_button {
                display: none !important;
            }
            .quantity {
                display: none !important;
            }
            .e-custom-product {
                display: none !important;
            }
        </style>
        <?php
    }
}
add_action('wp_head', 'hide_price_add_to_cart_and_quantity_for_accessories');

/**
 * Helper function to manage WC category terms where we want to hide the price
 *
 * @return array
 */
function itw_get_hide_on_terms()
{
    // Add multiple category names, slugs, or IDs if necessary
    // @see https://developer.wordpress.org/reference/functions/has_term/
    $hide_on = [
        'accessoires'
    ];

    return $hide_on;
}

/**
 * Remove price hook on archive pages
 *
 * @return void
 */
function itw_conditionally_remove_wc_shop_loop_pricing_hook()
{
    $hide_on = itw_get_hide_on_terms();
    if (is_tax()) {
        $current_term = get_queried_object();
        if (in_array($current_term->slug, $hide_on)) {
            remove_action('woocommerce_after_shop_loop_item_title', 'woocommerce_template_loop_price', 10);
        }
    } else {
        global $product;
        if (has_term($hide_on, 'product_cat')) {
            remove_action('woocommerce_after_shop_loop_item_title', 'woocommerce_template_loop_price', 10);
            // Need to re-hook as we can have products from different categories on different pages
            add_action('woocommerce_after_shop_loop_item', 'itw_readd_remove_wc_shop_loop_pricing_hook', 10);
        }
    }
}
add_action('woocommerce_before_shop_loop_item', 'itw_conditionally_remove_wc_shop_loop_pricing_hook');

/**
 * Readd the price hook to WC shop loop
 *
 * @return void
 */
function itw_readd_remove_wc_shop_loop_pricing_hook()
{
    add_action('woocommerce_after_shop_loop_item_title', 'woocommerce_template_loop_price', 10);
    // Removing this hooked function as we no longer need it
    remove_action('woocommerce_after_shop_loop_item', 'itw_readd_remove_wc_shop_loop_pricing_hook', 10);
}

/**
 * Remove price hook on single product pages
 *
 * @return void
 */
function itw_conditionally_remove_wc_product_pricing_hook()
{
    $hide_on = itw_get_hide_on_terms();
    if(is_product() && has_term($hide_on, 'product_cat')) {
        remove_action('woocommerce_single_product_summary', 'woocommerce_template_single_price', 10);
    }
}
add_action('woocommerce_before_single_product', 'itw_conditionally_remove_wc_product_pricing_hook');

/**
 * Remove product offer structure data.
 *
 * Commented out to fix Google rich snippet error.
 *
 * @return void
 */
function itw_conditionally_remove_wc_product_pricing_structured_data()
{
    if (function_exists('is_product') && is_product()) {
        global $product;
        $hide_on = itw_get_hide_on_terms();
        if(has_term($hide_on, 'product_cat')) {
            add_filter('woocommerce_structured_data_product_offer', '__return_empty_array', 9999, 1);
        }
    }
}
// add_action('template_redirect', 'itw_conditionally_remove_wc_product_pricing_structured_data');

/**
 * Remove product price from search suggestions
 *
 * @param string      $html    HTML string containing individual search suggestion
 * @param \WC_Product $product Current product object
 *
 * @return string Modified suggestion html
 */
function itw_conditionally_remove_theme_search_pricing($html, $product)
{
    $hide_on = itw_get_hide_on_terms();
    if (has_term($hide_on, 'product_cat', $product->get_id())) {
        $html = str_replace('<span class="price">' . $product->get_price_html() . '</span>', '', $html);
    }

    return $html;
}
add_filter('nm_instant_suggestions_product_html', 'itw_conditionally_remove_theme_search_pricing', 10, 2);
