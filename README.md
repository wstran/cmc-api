```typescript
import axios from 'axios';

/**
 * `CoinMarketCap ID Map`
 *
 * **Cache / Update frequency:** Mapping data is updated only as needed, every 30 seconds.
 *
 * **Plan credit use:** 1 API call credit per request no matter query size.
 *
 * **CMC equivalent pages:** No equivalent, this data is only available via API.
 */
export async function getCoinMarketCapIdMap({ listing_status, start, limit, sort, symbol, aux }: CoinMarketCapIdMap): Promise<{ data: ResultCoinMarketCapIdMap, status: number }> {
    try {
        return await axios.get('https://pro-api.coinmarketcap.com/v1/cryptocurrency/map', {
            headers: {'X-CMC_PRO_API_KEY': process.env.COINMARKETCAP_API!}, params: {listing_status, start, limit, sort, symbol, aux}
        });
    } catch (error: any) {
        return error.response;
    }
}

/**
 * `Metadata v2`
 *
 * **Cache / Update frequency:** Static data is updated only as needed, every 30 seconds.
 *
 * **Plan credit use:** 1 call credit per 100 cryptocurrencies returned (rounded up).
 *
 * **CMC equivalent pages:** Cryptocurrency detail page metadata like https://coinmarketcap.com/currencies/bitcoin/.
 */
export async function getCoinMarketCapIdInfo({ id, slug, symbol, address, skip_invalid, aux }: CoinMarketCapIdInfo): Promise<{ data: ResultCoinMarketCapIdInfo, status: number }> {
    try {
        return await axios.get('https://pro-api.coinmarketcap.com/v2/cryptocurrency/info', {
            headers: {'X-CMC_PRO_API_KEY': process.env.COINMARKETCAP_API!}, params: {id, slug, symbol, address, skip_invalid, aux}
        });
    } catch (error: any) {
        return error.response;
    }
}

/**
 * `Listings Latest`
 *
 * **Cache / Update frequency:** Every 60 seconds.
 *
 * **Plan credit use:** 1 call credit per 200 cryptocurrencies returned (rounded up) and 1 call credit per `convert` option beyond the first. **CMC**
 *
 * **Equivalent pages:** Our latest cryptocurrency listing and ranking pages like https://coinmarketcap.com/all/views/all/, https://coinmarketcap.com/tokens/, https://coinmarketcap.com/gainers-losers/, https://coinmarketcap.com/new/.
 */
export async function getCoinMarketCapIdListingsLatest({ start, limit, price_min, price_max, market_cap_min, market_cap_max, volume_24h_min, volume_24h_max, circulating_supply_min, circulating_supply_max, percent_change_24h_min, percent_change_24h_max, convert, convert_id, sort, sort_dir, cryptocurrency_type, tag, aux }: CoinMarketCapIdListingsLatest): Promise<{ data: ResultCoinMarketCapIdListingsLatest, status: number }> {
    try {
        const [response] = await Promise.all([axios.get('https://pro-api.coinmarketcap.com/v1/cryptocurrency/listings/latest', {
            headers: {'X-CMC_PRO_API_KEY': process.env.COINMARKETCAP_API!},
            params: {
                start,
                limit,
                price_min,
                price_max,
                market_cap_min,
                market_cap_max,
                volume_24h_min,
                volume_24h_max,
                circulating_supply_min,
                circulating_supply_max,
                percent_change_24h_min,
                percent_change_24h_max,
                convert,
                convert_id,
                sort,
                sort_dir,
                cryptocurrency_type,
                tag,
                aux
            }
        })]);
        return response;
    } catch (error: any) {
        return error.response;
    }
}

/** `string<date>` */
export type string_date = string;

export interface CoinMarketCapIdMap {
    /**
     * `Default: "active"`
     *
     * Only active cryptocurrencies are returned by default. Pass inactive to get a list of cryptocurrencies that
     *
     * are no longer active. Pass untracked to get a list of cryptocurrencies that are listed but do not yet meet
     *
     * methodology requirements to have tracked markets available. You may pass one or more comma-separated values.
     *
     */
    listing_status?: 'active' | 'inactive' | 'untracked';
    /**
     * `integer >= 1`
     *
     * `Default: 1`
     *
     * Optionally offset the start (1-based index) of the paginated list of items to return.
     */
    start?: number;
    /**
     * `integer [ 1 .. 5000 ]`
     *
     * Optionally specify the number of results to return. Use this parameter and the "start" parameter to
     *
     * determine your own pagination size.
     */
    limit: number;
    /**
     * `Default: "id"`
     *
     * Valid values: "cmc_rank" "id"
     *
     * What field to sort the list of cryptocurrencies by.
     */
    sort?: 'id' | 'cmc_rank';
    /**
     * Optionally pass a comma-separated list of cryptocurrency symbols to return CoinMarketCap IDs for. If this
     *
     * option is passed, other options will be ignored.
     */
    symbol?: string;
    /**
     * `Default: "urls,logo,description,tags,platform,date_added,notice"`
     *
     * Optionally specify a comma-separated list of supplemental data fields to return. Pass
     *
     * `"platform,first_historical_data,last_historical_data,is_active,status"` to include all auxiliary fields.
     */
    aux?: string;
}

export interface ResultCoinMarketCapIdMap {
    /** Array of cryptocurrency object results. */
    data: {
        /** The unique cryptocurrency ID for this cryptocurrency. */
        id: number;
        /** The rank of this cryptocurrency. */
        rank: number;
        /** The name of this cryptocurrency. */
        name: string;
        /** The ticker symbol for this cryptocurrency, always in all caps. */
        symbol: string;
        /** The web URL friendly shorthand version of this cryptocurrency name. */
        slug: string;
        /**
         * `[ 0 .. 1 ]`
         * 1 if this cryptocurrency has at least 1 active market currently being tracked by the platform,
         *
         * otherwise 0. A value of 1 is analogous with `listing_status="active"`.
         */
        is_active: number;
        /**
         * The listing status of the cryptocurrency. This field is only returned if requested through the aux
         *
         * request parameter.
         */
        status: 'active' | 'inactive' | 'untracked';
        /** Timestamp (ISO 8601) of the date this cryptocurrency was first available on the platform. */
        first_historical_data: string_date;
        /** Timestamp (ISO 8601) of the last time this cryptocurrency's market data was updated. */
        last_historical_data: string_date;
        /** Metadata about the parent cryptocurrency platform this cryptocurrency belongs to if it is a token, otherwise null. */
        platform: null | {
            /** The unique CoinMarketCap ID for the parent platform cryptocurrency. */
            id: number;
            /** The name of the parent platform cryptocurrency. */
            name: string;
            /** The ticker symbol for the parent platform cryptocurrency. */
            symbol: string;
            /** The web URL friendly shorthand version of the parent platform cryptocurrency name. */
            slug: string;
            /** The token address on the parent platform cryptocurrency. */
            token_address: string;
        };
    }[];
    /** Standardized status object for API calls. */
    status: {
        /** Current timestamp (ISO 8601) on the server */
        timestamp: string_date;
        /**
         * An internal error code for the current error. If a unique platform error code is not available the HTTP
         *
         * status code is returned. null is returned if there is no error.
         */
        error_code: number;
        /** An error message to go along with the error code. */
        error_message: string;
        /** Number of milliseconds taken to generate this response. */
        elapsed: number;
        /** Number of API call credits that were used for this call. */
        credit_count: number;
    };
}

export interface CoinMarketCapIdInfo {
    /** One or more comma-separated CoinMarketCap cryptocurrency IDs. Example: "1,2" */
    id: string;
    /** Alternatively pass a comma-separated list of cryptocurrency slugs. Example: "bitcoin,ethereum" */
    slug?: string;
    /**
     * Alternatively pass one or more comma-separated cryptocurrency symbols. Example: "BTC,ETH". At least
     *
     * one "id" or "slug" or "symbol" is required for this request. Please note that starting in the v2 endpoint, due to
     *
     * the fact that a symbol is not unique, if you request by symbol each data response will contain an array of
     *
     * objects containing all of the coins that use each requested symbol. The v1 endpoint will still return a single
     *
     * object, the highest ranked coin using that symbol.
     */
    symbol?: string;
    /** Alternatively pass in a contract address. Example: "0xc40af1e4fecfa05ce6bab79dcd8b373d2e436c4e" */
    address?: string;
    /**
     * `Default: false`
     *
     * Pass `true` to relax request validation rules. When requesting records on multiple cryptocurrencies an
     *
     * error is returned if any invalid cryptocurrencies are requested or a cryptocurrency does not have matching
     *
     * records in the requested timeframe. If set to true, invalid lookups will be skipped allowing valid
     *
     * cryptocurrencies to still be returned.
     */
    skip_invalid?: boolean;
    /**
     * `Default: "urls,logo,description,tags,platform,date_added,notice"`
     *
     * Optionally specify a comma-separated list of supplemental data fields to return. Pass
     *
     * `"urls,logo,description,tags,platform,date_added,notice,status"` to include all auxiliary fields.
     */
    aux?: string;
}

export interface ResultCoinMarketCapIdInfo {
    /** Array of cryptocurrency object results. */
    data: {
        /** The unique cryptocurrency ID for this cryptocurrency. */
        id: number;
        /** The name of this cryptocurrency. */
        name: string;
        /** The ticker symbol for this cryptocurrency, always in all caps. */
        symbol: string;
        /**
         * Valid values: "coin" "token"
         *
         * The category for this cryptocurrency.
         */
        category: string;
        /** The web URL friendly shorthand version of this cryptocurrency name. */
        slug: string;
        /**
         * Link to a CoinMarketCap hosted logo png for this cryptocurrency. 64px is defaul
         *
         * size returned. Replace "64x64" in the image path with these alternative sizes: 16,
         *
         * 32, 64, 128, 200
         */
        logo: string;
        /**
         * A CoinMarketCap supplied brief description of this cryptocurrency. This field will
         *
         * return null if a description is not available.
         */
        description: string;
        /** Timestamp (ISO 8601) of when this cryptocurrency was added to CoinMarketCap. */
        date_added: string;
        /** Timestamp (ISO 8601) of when this cryptocurrency was launched. */
        date_launched: string;
        /**
         * A Markdown formatted notice that may highlight a significant event or condition
         *
         * that is impacting the cryptocurrency or how it is displayed, otherwise null. A notice
         *
         * may highlight a recent or upcoming mainnet swap, symbol change, exploit event,
         *
         * or known issue with a particular exchange or market, for example. If present, this
         *
         * notice is also displayed in an alert banner at the top of the cryptocurrency's page
         *
         * on coinmarketcap.com.
         */
        notice: string;
        /** Tags associated with this cryptocurrency. */
        tags: string[];
        /** The unique CoinMarketCap ID for the parent platform cryptocurrency. */
        platform: {
            /** The unique CoinMarketCap ID for the parent platform cryptocurrency. */
            id: number;
            /** The name of the parent platform cryptocurrency. */
            name: string;
            /** The ticker symbol for the parent platform cryptocurrency. */
            symbol: string;
            /** The web URL friendly shorthand version of the parent platform cryptocurrency name. */
            slug: string;
            /** The token address on the parent platform cryptocurrency. */
            token_address: string;
        } | null;
        /** The self reported number of coins circulating for this cryptocurrency. */
        self_reported_circulating_supply: number | null;
        /** The self reported market cap for this cryptocurrency. */
        self_reported_market_cap: number | null;
        /** Array of self reported tags associated with this cryptocurrency. */
        self_reported_tags: any[] | null;
        /** The cryptocurrency is known to have an infinite supply. */
        infinite_supply: boolean;
        /** An object containing various resource URLs for this cryptocurrency. */
        urls: {
            /** Array of website URLs. */
            website: string[];
            /** Array of white paper or technical documentation URLs. */
            technical_doc: string[];
            /** Array of block explorer URLs. */
            explorer: string[];
            /** Array of source code URLs. */
            source_code: string[];
            /** Array of message board URLs. */
            message_board: string[];
            /** Array of chat service URLs. */
            chat: string[];
            /** Array of announcement URLs. */
            announcement: string[];
            /** Array of Reddit community page URLs. */
            reddit: string[];
            /** Array of official twitter profile URLs. */
            twitter: string[];
        }
    }[];
    /** Standardized status object for API calls. */
    status: {
        /** Current timestamp (ISO 8601) on the server */
        timestamp: string_date;
        /**
         * An internal error code for the current error. If a unique platform error code is not available the HTTP
         *
         * status code is returned. null is returned if there is no error.
         */
        error_code: number;
        /** An error message to go along with the error code. */
        error_message: string;
        /** Number of milliseconds taken to generate this response. */
        elapsed: number;
        /** Number of API call credits that were used for this call. */
        credit_count: number;
    };
}

export interface CoinMarketCapIdListingsLatest {
    /**
     * `integer >= 1`
     *
     * `Default: 1`
     *
     * Optionally offset the start (1-based index) of the paginated list of items to return.
     */
    start?: number;
    /**
     * `integer [ 1 .. 5000 ]`
     *
     * `Default: 100`
     *
     * Optionally offset the start (1-based index) of the paginated list of items to return.
     */
    limit?: number;
    /**
     * `number [ 0 .. 100000000000000000 ]`
     *
     * Optionally specify a threshold of minimum USD price to filter results by.
     */
    price_min?: bigint;
    /**
     * `number [ 0 .. 100000000000000000 ]`
     *
     * Optionally specify a threshold of maximum USD price to filter results by.
     */
    price_max?: bigint;
    /**
     * `number [ 0 .. 100000000000000000 ]`
     *
     * Optionally specify a threshold of minimum market cap to filter results by.
     */
    market_cap_min?: bigint;
    /**
     * `number [ 0 .. 100000000000000000 ]`
     *
     * Optionally specify a threshold of maximum market cap to filter results by.
     */
    market_cap_max?: bigint;
    /**
     * `number [ 0 .. 100000000000000000 ]`
     *
     * Optionally specify a threshold of minimum 24 hour USD volume to filter results by.
     */
    volume_24h_min?: bigint;
    /**
     * `number [ 0 .. 100000000000000000 ]`
     *
     * Optionally specify a threshold of maximum 24 hour USD volume to filter results by.
     */
    volume_24h_max?: bigint;
    /**
     * `number [ 0 .. 100000000000000000 ]`
     *
     * Optionally specify a threshold of minimum circulating supply to filter results by.
     */
    circulating_supply_min?: bigint;
    /**
     * `number [ 0 .. 100000000000000000 ]`
     *
     * Optionally specify a threshold of maximum circulating supply to filter results by.
     */
    circulating_supply_max?: bigint;
    /**
     * `number >= -100`
     *
     * Optionally specify a threshold of minimum 24 hour percent change to filter results by.
     */
    percent_change_24h_min?: number;
    /**
     * `number >= -100`
     *
     * Optionally specify a threshold of maximum 24 hour percent change to filter results by.
     */
    percent_change_24h_max?: number;
    /**
     * Optionally calculate market quotes in up to 120 currencies at once by passing a comma-separated list of
     *
     * cryptocurrency or fiat currency symbols. Each additional convert option beyond the first requires an
     *
     * additional call credit. A list of supported fiat options can be found [here](https://coinmarketcap.com/api/documentation/v1/#section/Standards-and-Conventions). Each conversion is returned in its
     *
     * own "quote" object.
     */
    convert?: string;
    /**
     * Optionally calculate market quotes by CoinMarketCap ID instead of symbol. This option is identical to
     *
     * `convert` outside of ID format. Ex: convert_id=1,2781 would replace convert=BTC,USD in your query.
     *
     * This parameter cannot be used when `convert` is used.
     */
    convert_id?: string;
    /**
     * `Default: "market_cap"`
     *
     * Valid values: `"name"` `"symbol"` `"date_added"` `"market_cap"` `"market_cap_strict"`
     *
     * `"price"` `"circulating_supply"` `"total_supply"` `"max_supply"` `"num_market_pairs"`
     *
     * `"volume_24h" "percent_change_1h"` `"percent_change_24h"` `"percent_change_7d"`
     *
     * `"market_cap_by_total_supply_strict"` `"volume_7d"` `"volume_30d"`
     *
     * What field to sort the list of cryptocurrencies by.
     */
    sort?: 'name' | 'symbol' | 'date_added' | 'market_cap' | 'market_cap_strict' | 'price' | 'circulating_supply'
        | 'total_supply' | 'max_supply' | 'num_market_pairs' | 'volume_24h' | 'percent_change_1h' | 'percent_change_24h'
        | 'percent_change_7d' | 'market_cap_by_total_supply_strict' | 'volume_7d' | 'volume_30d';
    /**
     * Valid values: `"asc"` `"desc"`
     *
     * The direction in which to order cryptocurrencies against the specified sort.
     */
    sort_dir?: 'asc' | 'desc';
    /**
     * `Default "all"`
     *
     * Valid values: `"all"` `"coins"` `"tokens"`
     *
     * The type of cryptocurrency to include
     */
    cryptocurrency_type?: 'all' | 'coins' | 'tokens';
    /**
     * `Default "all"`
     *
     * Valid values: `"all"` `"defi"` `"filesharing"`
     *
     * The tag of cryptocurrency to include.
     */
    tag?: 'all' | 'defi' | 'filesharing';
    /**
     * `Default: "num_market_pairs,cmc_rank,date_added,tags,platform,max_supply,circulating_supply,total_supply"`
     *
     * Optionally specify a comma-separated list of supplemental data fields to return. Pass
     *
     * `num_market_pairs,cmc_rank,date_added,tags,platform,max_supply,circulating_supply,total_supply,market_cap_by_total_supply,volume_24h_reported,volume_7d,volume_7d_reported,volume_30d,volume_30d_reported,is_market_cap_included_in_calc`
     *
     * to include all auxiliary fields.
     */
    aux?: string;
}

export interface ResultCoinMarketCapIdListingsLatest {
    /** Array of cryptocurrency objects matching the list options.. */
    data: {
        /** The unique CoinMarketCap ID for this cryptocurrency. */
        id: number;
        /** The name of this cryptocurrency. */
        name: string;
        /** The ticker symbol for this cryptocurrency. */
        symbol: string;
        /** The web URL friendly shorthand version of this cryptocurrency name. */
        slug: string;
        /** The cryptocurrency's CoinMarketCap rank by market cap. */
        cmc_rank: number;
        /** The number of active trading pairs available for this cryptocurrency across supported exchanges. */
        num_market_pairs: number;
        /** The approximate number of coins circulating for this cryptocurrency. */
        circulating_supply: number;
        /** The approximate total amount of coins in existence right now (minus any coins that have been verifiably burned). */
        total_supply: number;
        /** The market cap by total supply. This field is only returned if requested through the `aux` request parameter. */
        market_cap_by_total_supply: number;
        /** The expected maximum limit of coins ever to be available for this cryptocurrency. */
        max_supply: number;
        /** The cryptocurrency is known to have an infinite supply. */
        infinite_supply: boolean;
        /** Timestamp (ISO 8601) of the last time this cryptocurrency's market data was updated. */
        last_updated: string_date;
        /** Timestamp (ISO 8601) of when this cryptocurrency was added to CoinMarketCap. */
        date_added: string_date;
        /**
         * Array of tags associated with this cryptocurrency. Currently only a mineable tag will be
         * returned if the cryptocurrency is mineable. Additional tags will be returned in the future.
         */
        tags: string[];
        /** The self reported number of coins circulating for this cryptocurrency. */
        self_reported_circulating_supply: number;
        /** The self reported market cap for this cryptocurrency. */
        self_reported_market_cap: number;
        /** Percentage of Total Value Locked */
        tvl_ratio: number;
        platform: {
            /** The unique CoinMarketCap ID for the parent platform cryptocurrency. */
            id: number;
            /** The name of the parent platform cryptocurrency. */
            name: string;
            /** The ticker symbol for the parent platform cryptocurrency. */
            symbol: string;
            /** The web URL friendly shorthand version of the parent platform cryptocurrency name. */
            slug: string;
            /** The token address on the parent platform cryptocurrency. */
            token_address: string;
        } | null;
        quote: {
            /** A market quote in the currency conversion option. */
            [key: string]: {
                /** Price in the specified currency for this historical. */
                price: number;
                /** Rolling 24 hour adjusted volume in the specified currency. */
                volume_24h: number;
                /** 24 hour change in the specified currencies volume. */
                volume_change_24h: number;
                /**
                 * Rolling 24 hour reported volume in the specified currency. This field is only
                 *
                 * returned if requested through the `aux` request parameter.
                 */
                volume_24h_reported: number;
                /**
                 * Rolling 7 day adjusted volume in the specified currency. This field is only
                 *
                 * returned if requested through the aux request parameter.
                 */
                volume_7d: number;
                /**
                 * Rolling 7 day reported volume in the specified currency. This field is only
                 *
                 * returned if requested through the aux request parameter.
                 */
                volume_7d_reported: number;
                /**
                 * Rolling 30 day adjusted volume in the specified currency. This field is only
                 *
                 * returned if requested through the `aux` request parameter.
                 */
                volume_30d: number;
                /**
                 * Rolling 30 day reported volume in the specified currency. This field is only
                 *
                 * returned if requested through the aux request parameter.
                 */
                volume_30d_reported: number;
                /** Market cap in the specified currency. */
                market_cap: number;
                /** Market cap dominance in the specified currency. */
                market_cap_dominance: number;
                /** Fully diluted market cap in the specified currency. */
                fully_diluted_market_cap: number;
                /** Total Value Locked */
                tvl: number;
                /** 1 hour change in the specified currency. */
                percent_change_1h: number;
                /** 24 hour change in the specified currency. */
                percent_change_24h: number;
                /** 7 day change in the specified currency. */
                percent_change_7d: number;
                /** Timestamp (ISO 8601) of when the conversion currency's current value was referenced. */
                last_updated: string_date;
            };
        };
    }[];
    /** Standardized status object for API calls. */
    status: {
        /** Current timestamp (ISO 8601) on the server */
        timestamp: string_date;
        /**
         * An internal error code for the current error. If a unique platform error code is not available the HTTP
         *
         * status code is returned. null is returned if there is no error.
         */
        error_code: number;
        /** An error message to go along with the error code. */
        error_message: string;
        /** Number of milliseconds taken to generate this response. */
        elapsed: number;
        /** Number of API call credits that were used for this call. */
        credit_count: number;
    };
}
```
